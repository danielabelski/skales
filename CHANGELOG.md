# **Changelog**

All notable changes to Skales will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),

and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## v12.8.5 - Mirror

### Fixed

- **A cancelled upload no longer takes the server down with it.** Releasing
  the microphone, reloading the page mid-answer or letting a voice detector
  restart could end the whole Skales server, and with it every agent, the
  scheduler, the autopilot and any background work in progress. The cause sits
  in the runtime's own machinery: it finishes reading an abandoned request body
  by closing a stream twice, from a place no error handling can reach. That one
  fault is now recognised and outlived - written down, with its stack, so it
  stays visible - while every other kind of crash still ends the server the way
  it always has.
- **A released microphone stops costing money.** A transcription whose client
  had already walked away ran to the end at Groq, OpenAI, OpenRouter, Azure or
  Skales IQ, and when the disconnect looked like a refusal, the next provider
  was tried after it. A cancelled recording now cancels the call it started, and
  a cancellation is answered as one instead of as a failed transcription.
- **Pressing the microphone twice sends once.** Every voice surface - chat
  dictation, the chat microphone, Call mode, Iris and audio attachments - posted
  without knowing about the others or about its own previous run, so a fast
  second press or a restarting voice detector put two large uploads on the wire
  at once. A newer recording now replaces the older one cleanly and the replaced
  one leaves without an error message. Several audio files attached together
  still transcribe side by side.
- **The Telegram bot no longer disappears without a word.** An unexpected fault
  ended the bot process leaving nothing written down anywhere, so a restart
  looked like it had stopped for no reason. Every fault is now recorded with its
  stack, and the one stream fault above is survived there too - the bot sends
  media through the same machinery.
- **A tool that runs into a missing WordPress route says which plugin to
  update.** The message a user actually got was "the plugin is not active on
  that site", and it was the wrong advice: on a site that connects fine, an
  older connector simply does not carry the newer routes yet. That diagnosis
  never even reached the screen, because it was tested against the words of
  the message and WordPress does not put "404" in its sentence. Both halves
  are fixed - the reason is read where WordPress actually writes it, and a
  missing Skales route now names the plugin version to update to. It is only
  shown after both address forms have been tried, so a site on plain
  permalinks is never told this.
- **A page is not a post when Skales asks whether it may write.** The check
  that runs before every WordPress call asked for the post rights when the
  tool was about to touch a page, and for edit rights when it was about to
  delete. An account that may edit posts but no pages was waved through and
  then refused by WordPress itself, as a bare 403 the model could not read.
  Deleting and reading now also ask about the object they were actually given.
- **Skales names itself to the connector plugin.** The handshake carried the
  plugin's version but never the app's, so the plugin could not say whether
  Skales was the outdated half of the pair. It now does, and when the plugin
  says so, that sentence is shown next to the one about the plugin. Where the
  plugin publishes its route level, that number decides the message instead of
  the version string - so a site that already carries every route Skales calls
  is no longer asked to update for nothing.
- **Minimax pointed at two different countries at once.** The chat default
  addressed the old `api.minimax.chat`, while the Refresh Models button on the
  same card asked `api.minimaxi.com`, which is the mainland China deployment
  and refuses an international key. Both halves now address the international
  host. A base URL you entered yourself is untouched.
- **Developer, Docs listed tools that do not exist.** Fifteen entries carried
  invented names - among them `calendar_list_events` and `calendar_create_event`,
  which are really `list_calendar_events` and `create_calendar_event` - and one
  entry described a chat skill as a tool. Every name on the page is now the
  name the model answers to, and the safety each one promises is the one that
  is actually applied.
- **A failed WordPress connection says why it failed.** Test Connection and the
  WordPress screen both answered every failure with the same flat "Connection
  failed", no matter whether the site had rejected the token, answered 404, run
  into a timeout or handed back a firewall page. The real reason now reaches
  the screen, in the same words the WordPress tools have always used.
- **A site with plain permalinks connects.** WordPress only serves
  `/wp-json/` when pretty permalinks are switched on; with the default setting
  the identical route exists only under `?rest_route=`. Skales tried the first
  form and gave up. It now tries the other one when the first answers 404, and
  remembers which form that site speaks.
- **An outdated connector plugin is named.** An old plugin answered the
  handshake and then let every newer tool run into "not found". The version is
  now checked at the handshake and named on both screens, including the case
  where the plugin is too old to report a version at all. Nothing is blocked:
  what the old plugin can do, it still does.
- **Two new model profiles ship inside the app.** Muse Glimmer and DBRX were
  added to the shared profile library, but only machines that can reach GitHub
  received them - offline installs kept running both untuned, including Muse
  Glimmer 30B, which Skales' own local catalogue recommends as its agentic
  flagship. Both profiles are now built into the app itself.
- **Local speech now works on Windows and on Apple Silicon.** The engine behind
  dictation and read-aloud loads a native library chosen by platform, and every
  release since it arrived carried only the one belonging to the machine that
  built the package. A Windows install therefore held macOS libraries and no
  Windows one, and reported that the module could not be found - which read
  like a damaged download and was nothing a reinstall could ever repair. The
  Apple Silicon disk image had the same hole with an Intel library in it. Each
  package now carries the library its own machines can load, and no other, and
  a build that gets this wrong stops before it is packaged.
- **A speech engine that cannot load says why, and writes it down.** The old
  message named neither the cause nor the platform and left no trace in the
  system log at all. It now names the missing piece, says plainly that the gap
  is in the download rather than on the machine, and records one line in the
  log so a bug report has something to carry.
- **Google Calendar and Discord can be set up again.** Both cards lost their
  Configure button when the settings switches were unified, and the switch that
  was left behind ran the disconnect: the only control on the card asked whether
  you were sure and then deleted the connection, and nothing anywhere could
  open the panel holding the API key, the calendar IDs or the bot token. Both
  cards carry the full row again - a switch for the skill, Configure to unfold
  the setup, and Disconnect as its own red action with a confirmation. Browser
  Control lost the same button and had its switch wired to the panel instead of
  the skill, so it could be neither turned on nor off from its card; that one is
  fixed too. All fourteen collapsible setup panels in Settings are now checked
  before every build, so a card cannot go unopenable again.
- **Skales knew about YouTube uploads and never said so.** Publishing a video
  is a different Google permission from searching for one, and the capability
  report treated posting to YouTube and LinkedIn as always available whether
  anything was connected or not. It now reports the real state, and when your
  Google account was connected without the upload permission it says exactly
  that, and sends you to reconnect it with YouTube (upload) ticked instead of
  claiming Skales cannot upload at all.
- **Skales can now describe its own memory, and the rest of YouTube.** The
  long-term memory it keeps for you had no entry in the capability report at
  all, so asked what it remembers between sessions it had nothing to say while
  reading and writing that memory every turn. YouTube listed only search and
  kept quiet about video details, channel information, trending and caption
  tracks; browser automation left out attaching a file to a page. Every tool
  family is now measured against the report before each build, so a whole
  feature cannot go unmentioned again.
- **The WordPress tools arrive together.** With on-demand tool loading the 47
  WordPress tools were spread over four different groups and no group named
  WordPress, so a model asked to work on your site fetched whichever group
  sounded closest and got eleven of them, then reported the rest missing. They
  are one group now, called wordpress, and it says what is in it.
- **Generating a picture no longer ends the job it was part of.** An agent that
  made an image halfway through a task dropped a bare "Image generated!" line
  into the chat and stopped there, having never seen the result of its own tool
  - so the task it was given was simply abandoned, and asking it to carry on
  meant explaining the whole thing again. Making a picture is now a step like
  any other: the preview card still appears, and the same turn keeps going with
  the file it just produced. Video, speech and 3D generation already behaved
  this way and are unchanged.
- **A picture attached in a chat is a real file, and Skales says where it is.**
  Attachments were described correctly and then existed nowhere any tool could
  reach, so asking to post one, edit it or convert it failed on a file that
  looked like it was right there. Every attached picture is kept in the
  workspace and its location is now part of the conversation, so the tools that
  need the actual file can open it. Pictures sent from a phone or the browser
  interface were not being kept at all; they are now, in the same place and
  under the same name as on the desktop. The location travels with the
  conversation, so it still works after a restart.
- **One history, whichever agent you were talking to.** Starting a conversation
  with a custom agent replaced the sidebar with that agent's conversations only
  and offered no way back to the full list, while the conversation itself never
  appeared in the normal history afterwards. There is one list again, holding
  everything, and a conversation held with a custom agent carries that agent's
  emoji on its row. Opening such a row switches to that agent, so the reply
  comes from whoever the conversation was with. Narrowing to a single agent is
  still possible on the History screen, where it is a choice with a way back
  rather than a place to get stuck.
- **A shared visual keeps the shape it was designed in, and never lands
  blank.** A visual shared from Studio arrived in the feed without any note of
  the format it was built for, so a square, a widescreen and a portrait design
  were all shown in the same fixed box. The chosen aspect now travels with the
  visual and the feed lays it out accordingly - for visuals shared from a phone
  as well. And a visual whose frame had not been measured yet was scaled to
  nothing at all and read as an empty card; it now waits for a frame with a
  size before it fits itself into it.
- **A Playground space shared to Discover arrives with its text.** The
  description and the category were sent in a place the feed never reads, so
  the card appeared with an empty body under the wrong heading. Both now ride
  where the feed looks for them.
- **A voice preview no longer answers with a different voice.** Trying out a
  text-to-speech provider in Settings read the sentence through the full
  cascade, so a refused key, an empty balance or a dead endpoint all ended with
  the system voice speaking flawlessly and the preview looking like a success.
  The preview now stops at the provider that was picked and prints the reason it
  gave - the HTTP status, the refusal, the missing key - instead of handing the
  sentence on.

### Added

- **Settings > Voice > Fallbacks: every rule that swaps one engine for
  another, written down in one place.** Which voice steps in for which, in what
  order transcription is tried, what happens when the main chat provider fails,
  who reads an image sent to a model that cannot see, where a local image
  generator hands over, and what memory search does without embeddings. The
  voice rule can also be switched off there: with it off a reply stays silent
  and names the reason rather than being read by a voice nobody chose.
- **A rendered video can go to Discover, from every place Studio makes one.**
  Until now only pictures and Skales Visuals had a share: the cloud video, a
  Scenes render, a Type export and a Flow composition all ended at Download.
  Each of them now carries the same Share to Discover button the picture has,
  and an authored Flow page - a 3D scene among them - shares as the visual it
  is. A clip over 4 MB is refused before anything is posted and says so with
  the limit; a shared post waits for review the same way a shared picture
  does. Videos become visible in the feed once the feed server carries the
  matching upload path.

### Changed

- **3D comes back as a scene, not as a web page with a scene in it.** Asked for
  a 3D element, a language model writes a whole landing page around it: a
  headline, two paragraphs, a footer, and a small canvas in the middle. Skales
  now stages the canvas itself and hands the model the frame - a stage that
  fills the view, a camera, a three-point light rig, ground that takes the
  shadow, the resize handler and the loop - and says in as many words that a
  page around it is the wrong deliverable. What the model writes is the part
  that was ever the point: the object, the materials, the camera move and the
  mood. Chat, Studio and Flow ask for it in the same words.
- **Buttons that turn the thing still work.** "A cube with buttons that roll it
  and light it from the sides" is exactly the kind of request the rule above
  could have killed, so the frame has a place for it: controls float ON the
  canvas and let the finger through everywhere else, instead of standing in a
  document around it.

### Fixed

- **A turn that fails says what failed.** Whatever ended a chat turn - a model
  that ran past its time budget, an endpoint that refused the connection, a
  certificate this machine will not accept - the bubble said "Connection error.
  Please try again." and kept the real sentence, the one naming the budget or
  the address, out of sight in the session file. The reason now stands in the
  bubble, in the language the app is set to, with the raw text kept underneath
  it for the retry and the bug report.
- **Dragging a sidebar entry no longer shows a web address.** A slow press on a
  nav row and a small move started a browser link drag, and the ghost under the
  cursor read "http://localhost:3000/..." - the app naming its own dev server at
  the user. Nothing in the sidebar is something to drag, so nothing there drags.
- **"Reasoning off" reaches Ollama.** The setting travelled to every backend
  that has an off switch except the local daemon, where a thinking block is not
  a second of latency but a minute of it. It now goes out as the daemon's own
  parameter on both of its endpoints - the OpenAI-compatible one a text turn
  uses and the native one an image turn uses - so a Qwen or a Gemma stops
  thinking when it is told to. Older daemons that do not know the field answer
  the request unchanged, so nothing regresses on an install that predates it.
- **A connected MCP server's tools are reachable on a local model.** The list of
  tool groups a model can load on demand was written from the tools that
  survived the local model's tool budget instead of from the whole catalogue,
  and on a local model almost nothing survives that is not in the base set. The
  result: no groups were offered, none were registered, and asking for the MCP
  server's group came back "unknown group" for a server that was connected and
  enabled - so its tools were unusable until on-demand loading was switched off
  entirely. The index now describes what CAN be fetched, which was always its
  job, while the tool list still describes what this turn is carrying.
- **Web search set to MCP is honoured before the model chooses, not after it
  fails.** With the built-in search withheld, the model reached for it anyway,
  was told the built-in is set to MCP, and only then went looking - one wasted
  step, every time. Skales now names the connected search tool in the prompt,
  and a call that still arrives at the built-in is carried through to that tool
  instead of being refused. If no connected server actually exposes a web
  search, it says exactly that and names the way out.
- **Skales knows where its own tool-loading switch is.** Asked where to turn
  on-demand tool loading off, it sent people to Settings > Advanced, where there
  is no such control. It is under Settings > Memory, in the Memory Mode card,
  and that is now what the app knows about itself.

### Added

- **A shared Visual opens full screen.** Every Skales Visual in Discover, from a
  flat design to a turning three.js scene, has an expand button on its frame:
  it fills the window, takes the mouse or the finger, and closes with the button
  or with Escape. The card's own frame is put aside while it is open rather than
  thrown away, so closing carries on where it was instead of starting the
  animation over.

## v12.8.4 - Aware

### Fixed

- **A restart says it is a restart.** When the system ended Skales' background
  engine to free memory, the window went black with "Connection lost" and a Try
  again button, and nothing said that Skales was already starting itself back
  up. Every window now says what happened, which attempt it is on, and whether
  the engine was stopped by the system or fell over on its own - and when it is
  running again the surface comes back by itself, with nobody pressing Try
  again. If three attempts are not enough, it says that too instead of leaving
  you at the same button.
- **Skales stops denying what it can do.** Asked whether it could work with
  your WordPress site, it said no, although the connector has been there for
  months and forty-seven tools sit behind it. The same hole swallowed Obsidian
  and the ability to use your computer. All three are in its self-knowledge
  now, each one honest about whether it is actually connected: WordPress
  reports itself as ready once your site and token are stored, not before.
- **A fact said twice is one fact.** Telling Skales your name in three
  different conversations left three separate records saying the same thing.
  Near-identical facts are now folded into the record that was there first,
  which keeps the earliest date and counts how often it has been confirmed -
  visible on the Memory page. Facts that only look alike stay apart: "port
  8091" and "port 8093" are still two facts. What is already stored is tidied
  once, without anything being thrown away.
- **A document Skales writes is a document you can find.** Asking for a
  spreadsheet could report success and leave no file behind at all, because the
  packaged app cannot write the way the library wanted to. Fixed in the last
  release, and now checked on every build by actually writing a workbook and
  reading it back, so a tool that claims success and produces nothing cannot
  get through again.
- **A fresh install leads with what it can do.** Asked what it was capable of
  before any key was entered, Skales read out the long list of everything not
  set up yet. It now names what works right away first - Flow, Code Mode,
  visuals, your local files, the weather - and puts what a key would add into a
  single sentence with the place to enter it.
- **It knows it can draw a 3D scene without a provider.** Flow has written real
  three.js scenes for a while, but the ability was missing from Skales' own
  list, so the answer to "can you do 3D?" came from the model-generating tool
  that needs a provider and an account. It now says the true thing: the scene
  is written and drawn in the app itself, no key involved, and the way in is
  the 3D chip in Flow.
- **The assistant stops opening every answer the same way.** When the same
  short phrase has led three of the last few replies, it is asked to begin
  differently. Tone, warmth, persona and language stay exactly as they were;
  only the repetition goes.

### Changed

- **The shipped app is sealed.** Skales now ships in a hardened package.
  Nothing changes for you - the app installs, starts and updates exactly as
  before.

## v12.8.3 - Intact

### Fixed

- **A finished answer is no longer followed by a second one nobody asked for.**
  A line typed while Skales was still writing goes into the queue, as before.
  Two things then went wrong with it. A queued message that could not be handed
  over was put back, and putting it back is what woke the queue again, so the
  same text was submitted over and over; and the composer unlocks by itself when
  a turn stops responding, which the queue read as "the turn is over" while the
  turn was still running on the machine. Now a message is tried once more at
  most and then waits in plain sight with a Try again next to it, and nothing
  goes out until the run is genuinely finished rather than merely unlocked.
- **Long Telegram answers arrive whole.** Since 12.8.2 the answer is written
  into the message you are already looking at, and that path never checked
  Telegram's per-message limit: a long answer stopped mid-sentence at the last
  piece that still fit. A message that had waited in the queue was worse - it
  counted as delivered although Telegram had refused it, so it never arrived at
  all. Answers are now split and sent in order, with code blocks closed and
  reopened across the split, and nothing counts as delivered until Telegram
  confirms it.
- **Saving a mail account no longer destroys its password.** Changing any field
  on an existing account wrote the row of dots back over the real password. If
  an account is already in that state it now says so and asks for the password
  again, instead of reporting a login failure from the provider.
- **The mail Test button checks what is on screen.** It tested the saved values
  while showing the verdict as a verdict on the ones just typed, so a corrected
  port was reported as refused before it had been tried. Only the password still
  comes from storage.
- **Port and encryption belong together.** Switching encryption moves the port
  the way the label always promised, and the four impossible combinations say so
  at once instead of arriving later as a certificate error - which is also what
  implicit TLS on a STARTTLS port used to be reported as.
- **Skales answers correctly about its own integrations again.** A read-only
  mail account, or a settings file written by an older version, made the
  assistant deny Google, Drive, Docs and IMAP that were configured and working
  the whole time. Nothing had been removed: its self-check read different files
  than the tool catalogue and the settings screen. On local and small models
  those tools could also be squeezed out of the tool budget first; they are now
  protected once configured. A Google account missing one field now names the
  field instead of vanishing.
- **The context size you set for a local model reaches the engine.** It was
  never passed on, so the server chose a window on its own - 4096 on some
  machines - while the banner read your setting back to you rather than the
  window in force. The banner now shows the window the server actually holds,
  changing the setting restarts the local server, and a request that does not
  fit ends the turn once with the real numbers instead of being retried until
  the partial answer has been erased and restarted several times.
- **A local model no longer advertises reading pictures when it cannot.** The
  VISION badge is checked against the running engine, so a model whose projector
  is not on disk says so. An engine that is running with nothing loaded is no
  longer reported as not running, the role buttons say why they are disabled,
  and a start skipped because autostart is off says that too.
- **Raw `<textcall>` markup no longer appears in answers**, and - more
  importantly - the call inside it is now carried out. Removing the markup
  without running the call would have meant asking for something, and nothing
  happening, with no error.
- **Teams, Organization, Group Chat and Swarm are reachable again**, on every
  theme. The pages and the runner were never touched; only the doors to them had
  been taken out, and on the top-bar and icon-rail themes there was no way in at
  all.
- **One conversation can no longer lock the others.** Opening a chat kept the
  previous one's run, which held the input and the history list; starting a new
  chat was the only way out. A conversation that cannot be drawn now shows the
  reason and leaves the rest of the app alone.
- **Allowing something "for this session" holds across Chat, Code and Flow.**
  The three surfaces kept three separate notes of it, and in Code the answer
  could land in a copy the waiting run never saw, so the same question came back
  after it had been answered. In Flow a card showed "running" even when the
  answer could no longer be applied - after a restart, for instance; it now says
  the runner was lost and asks you to send again.
- **Rewrite (/spin) keeps what it produces.** The rewritten text was only put on
  screen, so the next refresh of the conversation removed it again - the feature
  ran and appeared to do nothing. It is written into the conversation now, and a
  write that fails says so and leaves the text where it can still be copied.
- **The Home/Chat switch shows the column that is on screen.** Opening a
  conversation raised the chat column while the switch still lit Home. Your
  stored preference is left alone.
- **Portrait pictures in Discover are shown whole** instead of cropped to a
  band.
- **Browser Control can attach a file to a web page.** Upload fields that a page
  hides are now listed and can be addressed directly, including through the
  button in front of them, so no operating-system dialog is needed; the field is
  read back afterwards and a mismatch refuses to submit.
- **Custom endpoints are first-class in the agent editor.** Each server you set
  up appears under its own name, the choice survives reopening and restarting,
  and an endpoint that has been switched off is named rather than shown as
  "Default".
- Memory "Minimal" now says what it does: it shortens the memory context, and
  the tool schemata have their own switch directly below it.

## v12.8.2 - Mood

### Added

- **Your Obsidian vault is a folder Skales lives in, not a copy it took.** The
  old import read your notes once and kept a snapshot, so everything you wrote
  afterwards was invisible while Skales still answered as if it knew. Settings,
  Integrations, Obsidian Vaults is where you point at the folder itself, give it
  a name ("Personal", "Work") and add as many as you keep. Every question reads
  the files on disk, so what Obsidian shows you is what Skales sees, and you can
  say "look in my Work vault" and be understood. The Memory page shows the note
  graph of whichever vault you pick, and offers to remove the old copy. The
  integration ships as a beta, and connecting the first vault asks one honest
  question first: what Skales reads goes to your selected model uncompressed,
  which costs tokens on long notes, and backups stay your job.
- **Skales can write into the vault, and never over it.** It can start a new
  note and add to an existing one, at the end or under a heading you name. It
  cannot replace a note: a create that would land on a note that already exists
  refuses and points at appending instead, so ten years of notes cannot be
  overwritten by a misunderstanding.
- **Where new notes land is a setting, not a request.** Target folder, filename
  pattern, note template and a tag on everything Skales files are yours to set
  under the vault list, with {title}, {date}, {time}, {content} and {tag} as
  placeholders. Leave them empty and a note lands in the vault root under its
  own title.
- **Your notes answer with what is in them.** Frontmatter and #tags are read
  along with the body, "tag:project" narrows a search to notes carrying that
  tag, and a note comes back with the notes that link to it and the ones it
  links out to.
- **Conscious: the mood it carries is in front of you while you work.** The
  companion's state was real and kept, but it was only ever readable on the
  Memory page, which is not where anybody is when the work is happening. There
  is a row of its own now, pinned above the bottom of the navigation and in view
  the whole time you are in a conversation. The colour is the mood, warm when
  the work is going well and cool when it is not, and how full it stands and how
  fast it moves is the energy. It changes while you are talking, not on the next
  restart. Hovering it opens a panel that says what the colour means, what moved
  it today with the times, and what is missing, where missing only ever means
  something that is in the record: a long silence, or a day with nothing
  finished yet. If interest tracking is on it lists what it keeps coming back
  to. It is a child of the companion switch in Settings, Memory, it draws and
  nothing else, and it never notifies, sounds or starts a conversation.
- **The mood keeps its day, and the things that move it can happen.** The state
  kept only the current value and threw every event away, so "what happened
  today" had no answer; there is a rolling log of the last two days now. Four of
  the seven things that can move the mood had no source in the code at all: a
  finished goal, a goal that did not get there, a run stuck on the same error, a
  long silence and a long stretch of work all register now. Resetting the mood
  on the Memory page clears the day's list with it.

- **Telegram answers in one message, and it writes as it thinks.** A question
  put to the bot produced a "Thinking..." line and then, some time later, a
  second and separate message with the answer in it, so a conversation with
  Skales on the phone read as twice as many messages as it had. The answer now
  grows inside the message that says it is working, the way it does in the chat
  window, and there is no second message at all.
- **A file Skales makes arrives as a file.** A spreadsheet, a PDF or a written
  document used to come back as a path in a sentence: unreachable from a phone,
  meaningless in a chat window on another machine, and a string to copy by hand
  here. The answer now carries a card with the name, the type and the size, a
  Download button and a way to open the folder. On Telegram the file itself is
  sent into the room the question came from, and a file the bot cannot send,
  because it is over the 50 MB the Telegram API takes, says so with its name and
  where it is instead of vanishing.
- **Saving a character with the companion switched off asks whether to turn it
  on.** The character shapes how Skales talks whether the switch is on or not,
  but everything it LEARNS - the mood, the topics, the working history - needs
  the switch. Saving a character while it is off now asks once, in a dialog,
  and takes no for an answer permanently. Nothing is turned on without the
  answer being yes.

### Fixed

- **Telegram answers once.** Every reply arrived twice, and the notice that a
  message is waiting arrived twice with it, while the send-a-message tool posted
  exactly once. Nothing was sending twice: two bot processes were reading the
  same chat. The lock meant to allow only one of them checked whether it existed
  and then wrote it, and two bots started in the same moment both read "no lock"
  and both went on to poll. Telegram hands the same message to both, and the
  repeat check that would have caught it lives in each process separately, so
  neither could see the other. The lock is now taken in one indivisible step,
  the loser stops instead of polling, and it only ever clears its own. Restarting
  never helped before because the start itself was the race: opening the app asks
  three places at once to make sure the bot is running, and each one started its
  own. They share one attempt now. There was a second double hiding under the
  first: an answer that had grown inside the Thinking message was posted once
  more as its own message, because the note saying it was already delivered was
  dropped on the way back to the bot. That note now travels with the answer.
- **A line typed while Skales is thinking goes into the queue.** During the
  thinking phase, before any text appears, pressing Enter left the sentence
  sitting in the box: not queued, not sent. The queue was real, the decision to
  use it just came too late, behind other work that could take the send first. A
  turn is running from the moment it starts, not from its first word, and from
  that moment every typed line is taken, the box clears, and what was typed is
  answered together with the rest. A stop command still goes straight through to
  the run it is meant to halt.
- **Opening Skales no longer arrives as a wave of pop-ups.** Everything that
  catches up when the app comes up, the briefing, the messages a channel held
  while it was away, the check-ins armed while it was closed, reached the
  screen at once in the first
  seconds, before there was any chance to read one. For the first minute after a
  start they are recorded and left on the Notifications page instead of firing.
  Nothing is lost and nothing is dropped, and anything urgent still comes
  straight through.

- **The button that adds your own endpoint opens something.** Under Settings,
  AI Providers, the "+ Custom endpoint" tile marked itself open, took the page
  to the bottom of itself and showed nothing at all, so there was no way left to
  add or edit an endpoint. The block it opens was classed as an Advanced-view
  section while the tile stood in both views, and a search that found the tile
  could miss the block the same way. The tile and what it opens are one thing
  now, in either view and under any search.
- **A model list that cannot be fetched says why.** Pressing Fetch Available
  Models on an endpoint that was not running answered "no models returned", an
  endpoint answering 404 or 401 was reported as a server that had never started,
  and an address whose name does not resolve said "fetch failed". Each of the
  five reasons now says what it is: nothing is listening, the name did not
  resolve, it took too long, the address answers but serves no model list, or it
  wants a key. An address typed without http:// is completed rather than
  refused, on every one of these paths.
- **The models an endpoint answers with are offered on it.** Fetch wrote the
  catalogue away and the endpoint kept a bare text box, so the button reported
  success and nothing on screen changed. The endpoint's own models are a picker
  now, with free text still there for a model the server does not list.
- **An endpoint you remove stays removed.** Removing the last extra endpoint and
  saving looked right until the next start, when it was back with the same
  identity, because an empty list was never written. As a consequence a
  perfectly ordinary Custom endpoint also grew a duplicate of itself in the
  endpoint list on every start. Both are gone, and removing an endpoint now
  clears the copy of it the app keeps for routing instead of leaving it behind
  for good.
- **An extra endpoint can be named wherever a provider is named.** It could be
  made active and handed to an agent, but the fallback chain, the advisor and
  executor, the per-mode overrides and the Code model offered only the built-in
  providers, so an endpoint could not stand in for the provider it was replacing.
  All five offer it now, and the "Custom model..." entry in four of those lists
  is translated instead of English-only.

- **Skales Local can see.** A model downloaded together with its vision file
  answered "I cannot look at images". The engine was being handed the models one
  by one with no way to know which vision file belonged to which, so every model
  loaded as text-only. They are paired now, and the card's size is the total of
  both. A vision model you brought yourself is paired as well, whatever the
  publisher happened to call its vision file.
- **A model you download is usable straight away.** The engine read the library
  once, at startup, so anything downloaded, imported or deleted while Skales was
  open was invisible to it until the app was restarted.
- **The local server stops when you stop using it.** It came up when you chose
  it and then stayed up for the rest of the session, holding gigabytes of memory
  for a provider you had since switched away from. Switching away now takes it
  down, switching back brings it up, and a server you started by hand is left
  alone.
- **Start and Stop are on the Skales Local tab.** The banner said the server was
  not running and offered nothing to press, while pointing at the desktop
  application you were already looking at. A start that fails now shows the
  engine's own last lines with it.
- **Deleting a local model unbinds it everywhere.** The file went, the routing
  went, and the copy of that choice on the provider card stayed - so a deleted
  model went on being the one a new chat loaded.
- **The reasoning control works on a local model.** It was inert. Its lowest
  rung now switches the thinking block off for that turn, which on a machine
  producing a few tokens a second is the difference between an answer and a
  budget spent entirely on reasoning nobody reads. On the phone, session titles,
  greetings and memory lines never think at all.


- **`/spin` runs when you pick it.** Typing `/spin` and pressing Enter chose the
  command out of the suggestion list and put it back in the composer, and
  nothing else happened - the rewrite only started on a second Enter. Picking a
  command that needs no words after it now sends it, which also brings back
  `/projects`.
- **Rewrite selection is reachable.** Selecting an answer with a triple-click
  and right-clicking it showed the whole-message menu: the selection technically
  ended below the bubble, so it counted as belonging to no bubble. A selection
  that starts in a bubble and picks up no text from outside it is that bubble's
  selection again.
- **The model you set as Main in Skales Local is the one that answers.** The
  chat read the provider card ahead of the Chat row of the matrix, so the row
  only ever took effect while the card was empty. The row also says, under it,
  which model the next turn will load.
- **Extra endpoints behave like providers.** "Fetch Available Models" stayed
  disabled with "Enter an endpoint URL first" on every endpoint after the first,
  because the check looked for the address in the wrong place. Agents can be
  pointed at a specific endpoint too: each one is its own entry in the agent
  editor, stored under its own name, and it is still there when the agent is
  reopened.
- **All four doors under the Flow composer.** 3D and the Video Editor were
  missing from Studio inside the app; they were never drawn there rather than
  cut off.
- **Typing in a long chat.** A keystroke in a three-hundred-message conversation
  took 150 ms and re-rendered every bubble to change nothing. It no longer does.
- **Text size is about the conversation.** The setting scaled code blocks, HTML
  previews and the reasoning panel with it, which is not what it says it does.
- **The line under an answer is filled in.** How long a turn took, how many
  memories it recalled and how many agents worked on it were shown for turns the
  window ran and never for the ones the server ran, and disappeared on reload.
- **A goal stops saying it is done when it is not.** A run could declare a goal
  finished while criteria nothing had ever confirmed were still open: only the
  ones naming a file or a command were checked, and everything else counted as
  agreed. An unconfirmed criterion now blocks completion, and when the run and
  the check disagree the goal comes to you with both views and an "Accept as
  done" button, instead of either closing quietly or being nudged in circles.
- **A finished goal names its evidence.** The closing line said "5 of 5 done";
  it now lists each criterion with what settled it, and accepting a goal
  yourself lists what the check could not confirm.
- **Projects are reachable when the agent is told it has them.** The system
  prompt named the four project tools outright while the request often did not
  carry them, so the model reported it had no access to projects. They now ride
  in the base set. CHECK CAPABILITIES also states which tools the current turn
  is actually carrying, measured on the request, rather than only what the
  registry has switched on.
- **Vision over your own OpenAI-compatible endpoint.** Three separate faults on
  one path: a model whose name ends in "-VL" (LFM2.5-VL and the whole naming
  convention) counted as blind, so the picture was removed before sending; an
  answer written as content parts rather than one string arrived as an empty
  reply; and a pasted ".../v1/chat/completions" was turned into a doubled path.
  A picture that cannot be sent now says which switch changes that, and
  "Skales Local" works as a Vision Provider.
- **A second skill of the same name opens its own page.** The menu entry was
  built from the name while the skill was stored under a de-duplicated id, so it
  pointed at the first skill.
- **Skill pages are sealed like widget pages.** A skill that returns HTML runs
  without access to the app around it and cannot load from outside sources; a
  blocked resource says so on the page instead of failing silently.
- **The Custom Widgets page speaks your language.** Its result and error
  messages were English regardless of the language setting.
- **Text with line breaks reaches the field it was meant for.** Typing put text
  into a page one key at a time, and every modern editor - X, Instagram, Reddit,
  a CMS - handles the Enter key itself, so paragraphs ran together into a single
  line. A 220-character post with six line breaks arrived as 214 characters on
  one line. There is now a step that places text in one go, line breaks and all,
  and it is what the assistant is told to use for anything longer than a search
  box.
- **Nothing is typed into a field that does not have focus.** Text used to be
  sent to whatever happened to be focused, and the check for where it landed
  came afterwards. The target is verified first, and a page that steals focus
  back gets a refusal that names what holds it instead of a write into nowhere.
- **Every write is read back, and the readback is the truth.** After writing,
  the field is read as it renders, line breaks included - the old check read a
  form that has no line breaks at all, so a broken write and its confirmation
  agreed with each other. The answer says whether the field matches exactly and,
  when it does not, which line differs first. There is also a step to read a
  field on its own, for when you want to confirm before submitting.
- **A dialog is no longer dismissed behind your back.** A confirm, an alert or a
  "discard this?" prompt was closed automatically and never mentioned, so the
  assistant only saw the aftermath. Those are now named in the result, and an
  in-page modal is named too, with the choices it offers.
- **The screenshot shows the action, not the moment before it.** Screenshots
  could be taken before the page had drawn the change, so the picture showed the
  previous state.
- **Browser and desktop control survive a tight tool budget.** Pressing a key
  and scrolling sat outside every priority tier, and so did all desktop control,
  which meant a small local model kept the tool that fills a field and lost the
  one that submits it.
- **Desktop control says when it is not allowed to run.** Without the macOS
  accessibility permission every click and keystroke silently did nothing and
  reported success; it now names the setting that fixes it. A click outside the
  screen is refused with the real screen size instead of reported as done, the
  pointer is checked afterwards, and typed text can be read back out of the
  focused field. Multi-line text types line by line, and on Linux the text is
  handed to the keyboard tool directly rather than through a shell that would
  reinterpret it.

- **The keyboard can see where it is in Studio.** Tabbing into the Flow composer
  or the project search moved the cursor there and changed nothing on screen:
  both fields switched the focus ring off and never put anything back, so a
  keyboard user had no way to tell which field they were typing into. Both now
  show the same ring every other field in Skales shows.

- **Shutting the computer down is not a crash.** Logging off or restarting
  Windows killed the Skales server mid-session, Skales immediately started a
  replacement that the ending session could not let start either, and both
  deaths were written down as crashes, with a "Server stopped" box on a machine
  that was already going away. A session that is ending is recognised now:
  nothing is restarted, no error box appears, and the entry is listed under
  ordinary shutdowns. Exit codes in the report are spelled out in words,
  including for reports written before this build.
- **A question from Skales stays answerable.** The card that asks you something
  was only live while it was the last thing in the conversation, so anything
  landing behind it, a message you typed, a follow-up run, a tool line from the
  same turn, killed its buttons for good and re-asking never helped. A question
  is live now until you answer it, in the chat and in the Code window, which
  read the same rule from one place. A turn that both draws an image and asks a
  question now asks it instead of ending on the image, and the idle prompt steps
  over an unanswered question instead of starting a second run under it.
- **Finishing a Google connection says what happened when it fails.** The screen
  answered "Failed to fetch", which is the browser's words for "Skales was not
  there", and it could hang on "finishing" indefinitely. It now says that
  nothing was connected and nothing was lost and where to start again, keeps the
  raw line underneath for a report, and gives up after a minute with its own
  sentence.
- **Crash recording is on in release builds.** The diagnostics report said
  "process crash recording is OFF in this build", and it was right in every
  release build there has ever been: the handlers hung on a hook that the
  pinned Next version does not run. They are installed from the heartbeat now,
  which runs in every packaged build. It writes to a local file and sends
  nothing.
- **The bug report tab speaks your language.** Any status the server sent that
  this build did not know fell back to hardcoded English, "resolved" was not
  known at all, and a status written with more than one hyphen was never
  matched. All of them are translated now, including the words the server uses
  for the same state, and the status colours come from the theme instead of
  fixed hex values.
- **Five whole screens stop answering in English.** Hugging Face provider
  settings, Spaces, Datasets, casting and the DeepSeek thinking controls were
  never translated, so 32 buttons and labels in the provider settings alone
  stood in English in all eleven other languages. 733 strings were translated,
  and a check now catches a value that is identical in all twelve languages so
  the next block cannot slip through the same way.
- **The error page's retry button reloads instead of crashing.** Pressing it
  mounted the router a second time in the same document, which is the one thing
  it cannot survive, so the recovery from an error was a second error. The Iris
  screen carried the same class of unguarded read and no longer does.

- **New starts a new chat, from both buttons.** The plus in the header opened the
  new-chat page while New in the conversation list emptied the conversation you
  were in without leaving it, and an emptied conversation had nothing to show:
  a screen-high blank that read as a crash. Reloading appeared to fix it, which
  only hid it, because the reload brought the old conversation back. Both
  buttons lead to the same place now, deleting the conversation you are in does
  too, and a wait long enough to notice shows a loading state instead of an
  empty screen.
- **The feedback box stops cutting long notes in half.** Everything past 4.000
  characters was silently thrown away as you typed, so a pasted paragraph
  arrived halved and nothing said so. The box now declines the extra characters,
  and once a note is long enough to be near the limit it says how many are left.
- **The mood says one thing, not two.** The word above the companion's mood bar
  and the highlighted line in its legend were computed from two different
  thresholds, so the panel could say "low-key" over a legend marking "good".
  There is one threshold now, and the sentence, the legend and what Skales
  itself is told all read it. The words changed with it, from instrument
  readings to how the work is going: rough, uphill, steady, good, flying.
- **Settings points at a page that answers.** Under Settings, Memory, the line
  offering to shape your companion's character led to the Memory page but not to
  the section it meant, and with the switch off that section answered with an
  empty card, so the trail ended in nothing. The section is a target now, it
  says what is on it, and it keeps the character form whether the switch is on
  or off.
- **Making a spreadsheet works in the installed app.** The tool has never once
  worked outside a development tree: the library it uses looks for its file
  access at the moment it writes, and that does not survive being packaged, so
  every attempt failed with a message about the folder not being writable. The
  folder was fine. Reading spreadsheets had the same fault.
- **A file asked for in a group arrives in that group.** `send_telegram_file`
  always sent to the paired chat, so a table someone asked for in a group
  landed in a private conversation instead. It goes to the room that asked.
- **Two quick messages on WhatsApp are one conversation, not two.** Nothing
  serialised them, so a second message while the first was being answered
  started a second agent running on the same conversation at the same time.
- **The queued-message banner hands back everything it is holding.** Pressing
  Send on it put the first queued message in the box and quietly dropped the
  banner with the rest still in it.
- **A file card stops offering a file that is gone.** The card is drawn from
  what the turn recorded, so a file deleted or moved since kept a full card and
  a Download button that answered with an error. Every card checks again.

### Changed

- **The free end of the model list is on the desktop too.** The three smallest
  models were listed for phones only and were therefore invisible on a computer,
  which is where most people try Skales Local first. The entry point is now
  530 MB instead of 2.5 GB, and it thinks, calls tools and reads images.
- **One row per model.** The same model appeared twice in the library under two
  names, once with its capabilities and once without.

- **Messages you send while Skales is working are answered together.** Each one
  used to become a turn of its own, so three impatient messages meant three
  answers to three halves of a question, and the wait multiplied. They arrive
  as one message now, in the order you typed them, separated by a blank line,
  with nothing added and nothing numbered. The chat window, a run on the
  server, Telegram, WhatsApp and the phone all do it the same way, and Telegram
  no longer sends a "position in queue" card per message.

## v12.8.1 - Rewrite

Two kinds of text get looked at in this release. The first is what a model
leaves in its own answers: the characters nobody can see and the typography that
reads as machine-written now come off, if you ask for it, and `/spin` writes a
text again in a plainer voice. The second is what Skales carries about you,
which is now something you can see, shape and delete: the assistant has a name,
a character you set with sliders, and a working history that grows out of the
work you actually did together.

Underneath it, shell works on a local model again, remote access can ask for a
second factor, autonomous workers can be your own agents, the local server
starts when you have chosen it, and the interface finished a long consolidation:
the controls, the type scale, the colours and the window chrome now come from
one place across the whole app.

- **Check-ins arrive again when the only thing stopping them was the mute.**
  Friend Mode read "on", named a channel and stayed silent, because muting live
  notifications quietly stops every scheduled check-in and nothing on the Friend
  Mode page said so. It says so there now, with the way to the switch. The Test
  button deliberately ignores the mute, so it also says when it just did -
  a green test over a blocked schedule sent people looking in the wrong place.
- **One notification is one notification.** A recurring reminder such as the
  morning greeting only started its own cooldown once a channel had actually
  delivered it, so while anything held delivery back - the mute, quiet hours, a
  frequency setting - it was recorded again on every heartbeat. A single
  greeting could fill the entire Notifications page and push everything else
  off it. The cooldown now starts when the notification is recorded.
- **Quiet hours and the frequency setting reach the in-app toast.** Toasts and
  their sound are a live channel, but they were rebuilt from the durable record
  and only ever consulted the master mute. A category set to "Once" still
  popped every time, and a notification during quiet hours still chimed at
  night, which left the master mute as the only switch that really worked. They
  follow the same settings every other channel does. The Notifications page
  still lists all of it.
- **Proactive notifications no longer double their emoji.** Seven of the eight
  carried it twice ("☀️ ☀️ Good morning!"): it was part of the text and added
  again on display.
- **A setting changed in the notifications popup stays changed.** The popup
  opens over the Settings page, so changing a Friend Mode option afterwards
  wrote back the older copy of your preferences and silently undid it.

### Added

- **Skales Visuals is something you can ask for.** The generator that designs a
  poster, a title card, an infographic or an animation as a real page was only
  reachable from the Studio screen, so asking the chat for a graphic got you an
  apology. It is a tool now: ask in the chat or in the Code window, and the
  finished design is drawn in the answer and saved to the same Studio gallery a
  Studio-made one goes to. Asking for a change ("wider", "more colour") changes
  that visual instead of designing a new one. It needs no image provider and no
  key.
- **3D, and the difference between the two kinds.** A three.js scene - lit,
  turning, drawn in the answer - now comes out of the same visuals path, and
  three.js is in the app, so no page has to fetch it. Separately, a real `.glb`
  model file can be made from a description or from a picture through Replicate,
  fal.ai, Meshy or Tripo, saved to your workspace, and turned and zoomed in the
  chat and in Studio. Skales says which of the two it is giving you: a scene is
  a picture, a model is a file you can open in Blender. Where WebGL is not
  available the page says so instead of going black.
- **Video Editor, stage one: the cut.** A timeline with a video and an audio
  track, clips from the Studio gallery and from your workspace, trim, reorder,
  delete, and export. It goes through the renderer Studio already uses - there
  is no second encoder - and an export that was interrupted comes back as
  finished or as stopped, never as a bar that says "running" for ever.
- **3D is something you can pick in Flow.** The composer has a "3D scene" mode
  next to Deck, Prototype and the rest: pick it, describe the scene, and Flow
  writes a real three.js page that the preview draws and turns. There is no
  provider and no key involved - three.js travels in the app - and a brief that
  says "3D" lands there on its own when the mode is left on Auto. A downloadable
  `.glb` model file is still the other thing, and still needs a provider; the 3D
  tile now says which of the two is which.
- **Two more doors in Flow.** 3D and the Video Editor sit beside Lio and Studio
  Classic, in the same tiles.

### Changed

- **The Widget AI form recommends instead of warning.** It opened with a red
  panel about an alpha phase, a corrupted setup and a backup to make first, for
  a feature that has been shipping for months. What is left is the part that was
  ever useful: an orange note that a premium or a coding model gives the better
  result. Twelve languages.

- **The video editor says it is in beta.** It carries the same amber badge
  Skales Local wears, both on the door in Studio and on its own heading, so
  what it is is clear before you walk in rather than after.

- **The character step no longer uses an internal name.** Setting up your
  agent's character said "Form your Zenit", which is what the mechanism is
  called in the source and means nothing to anybody else. The heading already
  says what the step is for, in all twelve languages.

- **Work that was invisible reaches Discover and the usage figures.** Designing
  a visual from the chat, making a 3D model and exporting a cut from the video
  editor all did their work and then appeared nowhere, while the same work
  started in Studio did. A designed graphic and a 3D model also stopped being
  filed as "images generated", which they are not: each has its own name in the
  feed now. Opening Analyze and answering a question card are counted as usage
  only and never reach the feed - what was asked, what was answered and what a
  session spent stay private.
- **The media tools ask what you have set up instead of announcing an order.**
  `generate_image` used to state its own cascade in its description - local
  ComfyUI, then A1111, then Skales Local, "otherwise Gemini or Replicate" - and
  Replicate was not in that sentence at all. So somebody with a Replicate key
  who asked for a picture was told about Gemini. And the tool that should answer
  "what can I make here" only knew about local backends, so a machine with cloud
  keys and no ComfyUI answered with an empty list, which reads as "not
  possible". There is one place now that works out what this machine can do -
  local backends, the built-in engine, every configured provider, Skales IQ and
  an endpoint you added yourself - and every media tool asks it. Name a provider
  you have and that one is used; name one you have not set up and Skales says
  so with the way to set it up, never a substitute and never "not possible".
- **three.js and GSAP travel with the app on the computer too.** The generator
  used to hand the model two CDN script tags. They are in the page before it
  runs now, on both the computer and the phone, and `cdnjs.cloudflare.com` has
  left the list of places a generated page may reach - on both.

- **Analyze: what a run spent, and whether anything went wrong.** Right-click any
  conversation in History or in the chat sidebar and pick Analyze, or click the
  spend bar under an answer or the context figure under the composer. A wide bar
  shows where the tokens actually went - system tools and system prompt, the
  conversation, what came back from the provider's cache, thinking, and the
  answer itself - and the fixed prefill is usually the largest slice by far,
  measured at the real payload rather than estimated. Under it the run is a log,
  one row per turn, foldable: the model, where the turn came in from, the tools
  in order with their arguments and results, the thinking text as thinking, the
  providers that failed before the one that answered, and a voice note with its
  transcription and the honest statement of where it was transcribed. Every turn
  carries a verdict derived from signals the run already recorded: ran clean, a
  provider failure, a model failure, or a failure of Skales itself, which gets
  its own tone rather than being filed under the model. Where the signals cannot
  separate the provider from the model, it says the cause is not clear instead of
  guessing. There is no grade, no score and no percentage. **None of this calls a
  model, and nothing leaves the computer** - it reads what the run already wrote
  down, so opening a report costs nothing. What a provider never reported is
  shown as not reported, which is a different thing from zero.
- **A typeface for the chat bubbles.** Settings, Appearance, above Text size:
  Inter, Lora, Comic Neue or Caveat. All four travel inside the app, so the same
  choice looks the same on every machine. It applies to the text of the bubbles
  and to nothing else - code, previews, tool cards and the thinking lid keep
  their own type, and code stays monospace under every choice.

- **Watermark: what comes off an answer before you read it.** A new section in
  Settings -> Chat & Code, off by default. **Remove invisible characters** takes
  out the zero-width spaces, text-direction marks and unusual spaces a model
  leaves behind, and changes nothing you can read. **Neutralize typography** is a
  separate switch because it edits visible text: the long dash becomes a comma
  and the one-character ellipsis becomes three dots, in Chinese and Japanese too,
  where the dash carries no spaces and a rule written for English prose finds
  nothing at all. **Straighten quotes** sits under it. Code blocks and inline
  code are never touched by any of them. What this does not do, and does not
  claim to do, is remove a statistical sampling watermark: that lives in the
  choice of words.
- **Rewrite, with `/spin`.** `/spin <text>` writes a text again in a plainer,
  more human voice; `/spin` on its own does that to the last answer, and the
  message menu offers the same action next to Copy. Pick the model that does it
  in the same Watermark section, or leave it on the one that is already
  answering; a local model keeps the text on your machine. The result always
  goes through the invisible-character pass, whether or not the switches above
  are on, because a rewrite model stamps its own markers in like any other.
- **Your assistant can have a name, and a character you set.** Settings ->
  Memory -> Companion, then **Shape your companion** on the Memory page: give it
  a name, pick a starting point (Companion, Colleague, Mentor, Sparring partner,
  Trickster, Quiet type), and move seven sliders - serious to playful,
  diplomatic to direct, dry to funny, polite to cheeky, speculative to grounded,
  and two new ones, businesslike to warm and answers-only to asks-back. All of
  it shapes how it talks to you in every conversation. A name is what you call
  it; it never claims to be a different model, and it still answers truthfully
  when asked what it runs on.
- **A working history that grows.** With the companion switched on, Skales
  counts the days you worked together, the messages and the goals you finished,
  and uses that to drop the introductions and use your shorthand instead of
  rebuilding context every time. It counts shared WORK and says so: it never
  claims to have missed you or to be fond of you, and a test walks every state
  it can reach looking for exactly that sentence.

- **A second factor for remote access.** Settings -> Security -> Remote access
  can now ask for a six-digit code from an authenticator app (Google
  Authenticator, Authy, 1Password) when a browser opens a remote session, with
  ten one-shot recovery codes for a lost phone. It is off until you set it up,
  and it is deliberate about what it covers: a browser signing in with the
  access URL is asked, and programs that send the token as a header - the mobile
  app, the relay, a headless instance, swarm peers - are not, so nothing that
  works today stops working. The card says that in as many words rather than
  implying more.
- **Dispatch from your own agents.** Settings -> Autonomy -> What a sub-agent
  starts with has a new option, **Pick from my agents**: tick the agents that
  may run as dispatched sub-agents and Skales chooses among those by name. Each
  brings its own instructions, skills and pinned model; the tool limits every
  autonomous worker has still apply to it unchanged.
- **The role presets are readable.** The same setting can now show, word for
  word, what a dispatched worker starts with: the tools every role gets, what
  the chosen role adds, whether it may dispatch workers of its own, what it can
  ask for at runtime, and what is never available to it.
- **Interests and mood.** One switch in Settings -> Memory, off by default.
  Skales tracks the topics you keep coming back to - at the level of the topic
  rather than the conversation, and down to which part of a topic - and carries
  a working mood between sessions. Both feed the messages it sends on its own,
  so a briefing or a Friend Mode note reads like it comes from someone who knows
  you. The mood is about the work, never about you, and it appears in a reply
  only where it fits.
- **Skales Local starts on its own** when this machine is the one you chose to
  answer: it is your active provider, or a row in the matrix is set to Main or
  Fallback. A chat that goes to a local model brings the server up if it is not
  running. Nothing points at this machine, nothing starts and no memory is used.
  **Start it on its own** at the top of the tab is the switch.
- **Rewrite a passage, not a whole answer.** Select any part of a reply, right
  click the selection, and the menu acts on the passage instead of the message:
  **Rewrite selection** first, then copy it, quote it, read it aloud or save it
  to a document. The gesture was already there and reached nothing; picking a
  paragraph out of a long answer no longer means retyping it after `/spin`. A
  selection dragged across several bubbles belongs to none of them, so the menu
  offered on each stays the one for the whole message.
- **Diagrams, drawn.** A ```` ```mermaid ```` block in an answer is now rendered:
  flowchart, sequence, state, class, entity-relationship, gantt, timeline, pie
  and xychart, in your accent and your theme, with the source one click away and
  a download as SVG. For a process, a structure or a comparison this is the
  cheapest good answer there is - four lines instead of a whole generated page -
  and unlike a page it is also the form a small local model gets right. The
  renderer is in the app; nothing is fetched.
- **Code blocks have colour.** Comments, strings, numbers, keywords and markup
  tags are tinted in every fenced block in the chat, in about fifty languages,
  using the same tokenizer the Code window's review panel already used. The five
  colours are measured against the surface they sit on rather than chosen by
  eye, and a gate holds them above the readable floor.
- **A page in an answer is shown as a page.** The chat drew a live preview only
  when the model wrote exactly `html` after the backticks. A block tagged `htm`,
  `svg`, `xhtml` or `html5`, or one that simply begins with a document, arrived
  as grey monospace - which is why the same answer from the same model was a
  drawn page on the phone and source code here. The rule is now the phone's, and
  a test compares the two so they cannot drift apart again. A snippet that
  merely mentions a tag stays code, and a `text` or `xml` fence is still the way
  to hand over markup you want to copy.
- **Skales knows what its own chat can do.** The system prompt described tools
  and providers and never the renderer, and the one sentence that said "put a
  page in a code block and it is drawn" sat in Code mode and nowhere else - so
  whether an explanation arrived as a picture or as three paragraphs came down
  to the habits of whichever model was answering. It now carries the live
  preview, the diagram, the formula and the colouring in every mode, with a test
  that checks each claim against the component that has to honour it.

- **A widget can be an application now, not just an answer.** Custom Widgets
  have been in Skales since v1, but a widget was an `execute()` function whose
  return value got drawn in one of four shapes - a gallery, a table, a block of
  HTML, or text. Nothing it produced survived being looked at twice, so the
  obvious thing to build, a notes app that keeps your notes, could not be built.
  There is now a fifth shape: a page you wrote, running in its own sandbox, with
  a store of its own. The four existing shapes are untouched and every widget
  built before this one still works exactly as it did.
- **The store a widget keeps.** A widget gets `window.skales.storage` with four
  calls - `get`, `set`, `remove`, `keys` - and that is the entire surface it gets
  from Skales. Deliberately not `localStorage`: that lives in the page's own
  origin, so it disappears when a cache is cleared, sits outside the folder a
  backup walks, and cannot be put back on import. This store is a file next to
  the widget's page, which means your entries survive a restart, travel in an
  export, and come back on the other side.
- **Widgets travel.** A backup now carries a `widgets/` section - the folder, the
  page and the store, per widget - and it is listed as mobile-compatible, so a
  widget built on the computer opens on the phone and back again. An import that
  brings a widget you already have never writes over it: the widget on this
  machine is left exactly as it is, the incoming copy is kept beside it, and the
  import names both. The store is your entries, and replacing it silently would
  be the expensive kind of quiet.
- **The house typography.** Twenty families across the six directions the design
  rules ask for - display, serif, grotesk, condensed, mono and script - now ship
  inside the app as subset woff2, and only the ones a page actually names get put
  into it. A generated page gets its typeface instantly, offline, and identically
  on both platforms. Naming a different Google family still works; the bundle is
  the guaranteed path, not the only one.

### Fixed

- **Analyze opens on the conversation you are actually in.** Clicking the spend
  bar under an answer, or the context readout under the composer, said there was
  no conversation to analyze - while you were sitting in one. Only the
  right-click in the session list worked, so the same conversation had a report
  by one route and nothing by the other. Both of those two ways in ask for
  "whatever is open right now", and the answer to that question was being read
  off a yes/no flag, which could never name a conversation. All three ways in
  reach the same report now.

- **Analyze and the figures under the answer are visibly the same numbers.**
  They always were: the first three parts of the bar are the input the provider
  counted, the last two are the output. Nothing said so, so a breakdown reading
  24k / 477k / 4.5k next to a bubble reading 501K in / 4.5K out looked like two
  different measurements with no way to tell which one to believe. The
  distribution now writes both totals out and says they are the same two
  figures.

- **A conversation that came in over WhatsApp, Telegram or Iris says why it has
  no figures.** It was being told it had been written before Skales kept usage
  figures, which for a conversation from this morning is simply untrue. A
  channel hands Skales a message and takes the answer back and reports no usage
  of its own; that is what it says now, and it names the channel.

- **The full-screen panels no longer sit on the window buttons.** Analyze,
  History, Group Chat and the rest opened flush against the top of the window,
  which on macOS is where the close, minimize and zoom buttons are drawn. People
  reached for what looked like the panel's own controls and quit Skales. The
  panels start below them, and so do the X and "Back to Flow" buttons in the
  Flow window, which were pinned four pixels from the same edge.

- **Port selection tests the address the server will actually use.** The check
  that walks ports 3000 to 3009 bound the loopback address while the server
  itself binds every interface whenever remote access or Swarm is on. On macOS
  those two can coexist, so the check reported the first port free, the real
  start died on it, and the nine spare ports were never tried. With remote
  access off it was the quieter half of the same fault: a second copy of Skales
  could take over the first one's address in silence.

- **The token readout under the composer can be read again.** Hovering it raised
  the operating system's own tooltip directly over the card carrying the exact
  figures, and the card could not be moved onto with the mouse. The tooltip is
  gone, what clicking does is written in the card itself, and the card can be
  hovered and its numbers selected.

- **The spend figures appear at the bar, not under the pointer.** The model line
  under an answer and the spend bar in the bubble explained themselves through
  the operating system's own tooltip, which is drawn about twenty pixels below
  the cursor and never beside the thing it explains. Both now open the same
  hover card the rest of the composer uses, anchored above the figure.

- **The whole context card opens the report it offers.** It ends with "click for
  the full report" and was the one part of the readout that did nothing when
  clicked; only the "... context" line underneath worked. The card is the button
  now, and the figures in it can still be selected and copied.

- **"Code" in the menu no longer glows.** It carried a ring that faded in and out
  every few seconds until the entry had been opened once, so on a machine where
  nobody opens Code it never stopped, and it read as a fault rather than an
  invitation.

- **The four Flow doors are one shape.** Lio AI, Studio Classic, 3D and Video
  Editor were laid out one, three or four across depending on the window width,
  with a title long enough on one of them to make it a different height from the
  rest. They are two by two, the same size, with the same amount of text. The Lio
  door is called Lio AI; what it does is the line underneath. 3D wears the beta
  badge alongside Video Editor, because it can do about as much.

- **The personality questions stop after one.** Five to seven "a quick question
  for you" cards could land in a single conversation, each with its own toast, and
  a card dropped into an old conversation while you were away marked it unread.
  There is one per conversation now and one announcement with it, the mark is
  gone, and the ordinary clarifying question the assistant asks while working -
  which is not this feature at all and was raising the same notice - is quiet.

- **A long conversation costs less to draw.** The spend breakdown behind each
  answer's bar was recalculated for every answer in the conversation on every
  redraw, and a conversation redraws on every word of an answer being written.
  It is worked out once per answer now.

- **The chat typeface reaches the answer, not only your own message.** Choosing
  a face changed the text you wrote and left everything Skales said in the
  default one, which made the setting look half-broken. An answer that carries
  reasoning - which is every answer from a thinking model - was drawn outside
  the place the choice applies. Both kinds of answer follow it now, at the size
  that belongs to the face. The reasoning lid deliberately stays in the default
  face: it is not the answer.

  The same setting was still stopping at your own message whenever the answer
  had run a tool, which in practice is most answers: an answer with tool results
  and an answer waiting for approval are drawn by a different piece of the chat,
  and neither of them knew about the choice. All of them follow it now. The size
  is a multiplier, so no bubble is allowed to sit inside a second one and apply
  it twice.

- **A designed page that animates in is visible even when the animation never
  starts.** A poster or a visual is usually written to fade and slide into
  place, which means every element begins at zero opacity. When whatever was
  supposed to bring them up did not run, the result was not a page missing its
  motion - it was a blank rectangle, impossible to tell apart from a page that
  failed to generate. Every page is now watched as it opens: if nothing at all
  is on screen shortly after it loads, its opening is triggered a second time,
  and if it is still empty after that, it is shown in its finished state. Pages
  made earlier are covered too, because the check happens when a page is shown
  rather than when it is written.

- **A question in the Code window is answerable again.** A question with no
  options to pick from drew no field at all, and the send button demanded a
  choice for every question - which a free-text question can never give - so the
  card sat there greyed out and the composer was the only way past it. There is
  now a proper field wherever there is nothing to pick, every option list ends
  in a "Something else" escape with a field behind it, and a typed answer counts
  as an answer. With more than one question on the card, the footer says which
  one the number keys are currently pointing at and that question is marked, so
  a 2 is never a guess. The composer still answers everything, as before.
- **The answer to a question card looks like the answer to a question card.**
  What you picked used to come back as a numbered list in a plain message
  bubble, so the question was a card and the reply to it was a paragraph. The
  reply now draws as the pair to the card, one row per question with the choice
  under it. Nothing changed about what is sent: the model still receives the
  questions and answers in full, in plain text, which is the only record of the
  exchange once a conversation has been compacted.
- **Shell survives every tool budget.** On a local model, `execute_command`
  could be trimmed out of a turn while the first line of the system prompt still
  promised a shell, so the model reported - accurately - that it had no shell
  tool. The shell family is now protected alongside the file tools, and the
  Shell line only appears when the tool was actually sent.
- **A local model is no longer planned against a window four times too small.**
  The per-turn budget assumed 8192 tokens of context for any local runtime, even
  though LM Studio reports its real window and a custom endpoint was not in the
  table at all. Measured on the same catalogue, LM Studio went from 7 tools per
  turn to 88 and a custom endpoint from 7 to 120. A real rate limit still binds:
  a key on an 8000-tokens-per-minute plan is planned for exactly as before.
- **The trim note points at the setting that did the cutting.** Three different
  limits can shorten the tool set and the note always named the local-model
  slider, which changes nothing for two of them.
- **The IMAP account test works.** Testing an account against a server that
  sends folder names in modified UTF-7 (Yandex among them) failed with
  `allocateBase64Buffer is not defined` from inside a minified chunk. The
  library it comes from is written for plain CommonJS and was being bundled into
  a strict-mode chunk, where the helper it needs stops existing. It now loads as
  what it is.
- **The IMAP and SMTP test buttons always answer.** A server that accepted the
  login and then went quiet left the button spinning with no error and no
  result; both now stop after 45 seconds and say what happened.
- **A custom agent no longer runs on a model its provider does not have.** An
  agent that stored a model id while one provider was active kept it after a
  switch, and the id travelled to an endpoint that never heard of it. The stored
  id is now checked against the provider's own catalogue, and only a catalogue
  that actually answers can replace it.
- **Skales Local failures say what they are.** A local server that is not
  answering, a machine that ran out of memory loading a model, and a model that
  cannot read images are three different sentences with three different next
  steps, instead of a transport error.
- **AIPointer's switches are reachable by keyboard and named for a screen
  reader.** They were neither.

- **A goal now remembers the files it produced.** The ledger has always had a
  place for them and nothing ever wrote one, so the list was empty everywhere it
  was read: the checker's evidence, the "Produced:" line of a distilled strategy,
  and the durable half of the did-any-work test. A run could write a file, have
  its raw turns compacted away, and then have nothing left that remembered the
  file existed. Every successful write is recorded on the goal now.

### Changed

- **Code in a chat takes its colour from the theme.** The dark surface behind a
  code block was written into three separate places as a fixed value, so no
  theme could reach it. It is one setting now, next to the five colours the code
  itself is written in - and those five are measured against it, so a theme that
  moves the surface cannot leave the text unreadable on top of it.

- **Interests and mood moved to Settings, Memory, and what they learn is
  visible.** The switch sat in General under a heading about units and the
  weather, and the settings search could not find it: looking for "emotion"
  returned nothing. It is a section of its own under Memory now, where it
  belongs, and the Memory page shows every topic Skales has noticed, how often
  it came up and which narrower part of it recurred. Each one can be deleted on
  its own, all of them at once, and the mood and the working history each have
  their own reset. Deleting is real deleting: a removed topic only comes back if
  it comes up again. A memory you can neither read nor erase is not a feature.
- **The chat's popups open full width.** Agents, Group Chat, Teams,
  Organization, History, Projects and Add-Ons opened into a dialog the width of
  a document, which is about half of what a team roster or an org chart was
  drawn for. They now open in the same frame the Cockpit uses, and History is
  one of them rather than a navigation away from the conversation. The width
  comes from the host, so the same screen reads full width in the popup and as a
  document on its own route.
- **One interface, from one place.** Over several passes the app finished a
  consolidation that had been running for a while: every toggle, select, slider
  and API-key field now comes from one shared component, the small font sizes
  have names instead of arbitrary pixel values, status colours and accent
  colours come from tokens so all six themes are correct, every document page
  shares one content width, and window chrome and the brand typeface are the
  same on every surface, including the splash, error and OAuth screens. Studio
  speaks the same base as the rest of the app again.
- **Two switches showed two states at once.** A stylesheet rule left over from
  an older kit drew a second, fixed knob under every shared toggle; on, the two
  drifted apart.
- **Slider legends sit on their real value.** A three-mark legend was spaced
  evenly regardless of the scale, so "5 min (default)" sat in the middle of a
  one-to-sixty-minute track where half an hour actually is, and the last mark
  stood past the end of the track it labelled.
- **One background per surface.** The chat landing page, Wrapped, the DevKit
  docs page and the sidebar popup each laid a second background over the app.
- **Clean up under Skales Local actually deletes.** It skipped anything that was
  not a file and reported success.

- **Model-written pages have a stated network posture, and it is the same one on
  both platforms.** The preview iframe in chat had no content policy at all, so a
  generated page could load from anywhere. It now carries one, and it allows
  exactly the three places the generator prompts themselves point at:
  `cdnjs.cloudflare.com`, Google Fonts, and `images.unsplash.com`. Everything
  else is refused, and the refusal is printed on the page with the address that
  was tried, rather than failing as a blank space that reads like a bug in the
  page. A Custom Widget gets a stricter posture still: no network at all, on
  either platform, so a widget behaves the same wherever you open it.
- **The widget builder can build the new shape.** Widget AI now knows about pages
  with state: it is told the four storage calls, that it has no network, and
  which typefaces are already loaded. Rebuilding a widget keeps its folder, so a
  rewrite of the page is not a way to lose the entries you had.

## v12.8.0 - Sightline

The sidebar is one list again, the surfaces that had grown into a menu of their
own are back where the work happens, and what Skales knows about you can be kept
off the network with one switch.

### Added

- **One sidebar, and it belongs to what you are doing.** Home lists Dashboard,
  Chat, Code, Studio, Cockpit, Planner, Iris, Memory, Mobile, then Discover and
  Wrapped, then System. Open a conversation and the same column becomes the
  chat's own navigation: New chat, Agents, Cockpit, and a More group holding
  History, Projects, Teams, Group Chat, Organization, and Add-Ons and Skills.
  The shortcuts open as overlays over the same screens the routes show, each
  with its own title bar, so nothing sits under the close button any more.
- **Cockpit: everything that is running, in one place.** Goals, Tasks and
  Schedule as three tabs of one screen, with the tab in the address so a reload
  brings you back where you were. Goals has a screen for the first time: what
  each goal is trying to do, the criteria it must meet and the evidence it has
  collected, its last steps with the tool each one used, what it produced, what
  it learned, and what it has spent, with Open, Continue and Stop on each one.
  Finished goals are listed too, and so are the ones on a repeating schedule.
- **Studio opens Flow.** Clicking Studio puts you straight into the workspace
  in its own window instead of a screen asking which half you want. The whole
  of the older Studio is still there, one door below the composer as **Studio
  Classic**, on the tab you were linking to, with a Back to Flow line at the
  top of its rail. A second door starts a prototype in Lio AI, and that page
  has its way back too.
- **Planner has its own entry again.** It is the one visual calendar surface
  and it survives a fresh install.
- **A Codework mode in Code.** A pill in the composer starts the session with
  the file and preview panel and the review panel already open, so the chat,
  the files and the diff stand side by side from the first message. Pressed or
  not pressed is remembered like the terminal and the panel widths, and it
  shapes a session when it starts rather than rearranging one mid task.
- **Code takes a typed project path,** so a folder can be opened without the
  native picker, and the server validates it the same way either way.
- **Code drafts its own commit message.** A Draft message button beside Commit
  reads the staged diff and writes the message; a repository without a first
  commit is handled rather than failing on a missing HEAD.
- **Chat and Code can see your Projects,** and can keep their own working notes
  in one instead of scattering scratch files.

### Changed

- **Privacy Mode: what Skales remembers about you never leaves the machine.**
  One switch. With it on, a cloud model gets no memory index, no name, no
  facts, no preferences, no interests and no learnings, and the prompt says
  plainly that a memory exists and is being kept local rather than pretending
  there is none. Your language and the clock survive, local models are
  unaffected, and the background briefing follows the same rule. Which
  provider counts as local is decided by the address it points at, never by
  its name, so a custom slot on a rented endpoint is treated as the cloud it
  is.
- **The prompt is budgeted before it is sent,** so a model with a small context
  window is given a tool set it can actually hold instead of one that pushes
  the conversation out, and it is told what is not currently loaded rather than
  being left to guess.

### Fixed

- **A goal is only finished when its criteria are met.** The chat used to be
  able to write a goal off as done past the gate the runner honours. Both paths
  go through the same gate now. Accepting a result may overrule the criteria,
  but never a sub task that is still running, and a refused accept leaves the
  goal parked and says on the card why.
- **A completion marker never appears in the answer.** The word the runner uses
  to signal it is finished is stripped from what you read, on every surface.
- **Updates that stall say so.** The updater notices a download that stopped
  moving instead of leaving a progress bar sitting at a number forever.
- **Unsloth Desktop, and every other provider, gets the address it documents.**
  Fetching models from a cloud provider whose default address carries no path
  appends the version segment those providers resolve to anyway, and the bare
  address is still tried second. A conformance check now walks the whole
  provider registry so one card can no longer build its URL differently from
  the rest.
- **The Obsidian theme has all of its menu.** Its dropdown had been dropping
  every entry that was not in a group, which since the sidebar was rebuilt
  meant Agents, Tasks, Schedule and Notifications had no door at all.
- **Settings opens on the section a link names,** instead of landing at the top
  of the page and leaving you to find it.
- **New chat no longer reloads the history list** every time it is pressed.

## v12.7.45 - SKLS

A repair release, built the day after 12.7.4 from what the first devices
reported. The local server that the release was named for now answers on the
machines it was promised to, and a handful of screens that guessed, hid or
misnamed their failures say what actually happened instead.

### Added

- **Unsloth Desktop is a provider with its own name.** It has a tile in the
  provider grid with the same on and off switch every other provider has, an
  address already filled in, and a button that finds a running instance on
  either of the two ports it uses. It is the first local runtime that wants an
  API key, so the card says where that key is made, and a refused key is
  reported as a refused key instead of as a missing model. Refresh Models reads
  the runtime's own GGUF catalogue with the quantisation of each file, and a
  model that is still loading gets the long silent window a local runtime needs.
  How long a model stays in VRAM is set inside Unsloth Desktop, and the card
  says so rather than showing a control that would do nothing.

### Fixed

- **A turn to the local server carries a local model id.** Switching to Skales
  Local kept the previous provider's model id, so the runtime was asked for
  "openai/gpt-5-mini" it could never hold. Both request paths now resolve the
  id from the capability matrix, the provider card or the runtime itself, in
  that order, and a request to a local address can no longer carry another
  vendor's id.
- **The Mac build for Apple Silicon carries the local server.** The arm64
  package held 253 MB of engines for three other platforms and none of its
  own. Each platform and architecture now stages its own engines, a build
  gate measures the staged files against what the configs promise to ship,
  and every bundled library had its build-machine search path repaired, not
  just the executable. Mac packages shrink by the foreign payload they no
  longer carry.
- **Fetch Available Models sees what the runtime holds.** The fetch button
  knew one URL and two answer shapes while the error path knew two and three,
  so the card said "0 models" while the chat error listed all of them. One
  reader serves both now, the card offers its own library without a button
  press, and projector companion files no longer appear as answerable models.
- **Models on another disk are found.** Detection read two default folders on
  the system drive and called the rest of the computer empty. It now reads
  Ollama's configured model path including the Windows user environment, LM
  Studio's own settings, and asks running runtimes what they hold; an empty
  scan names the folders it read and offers picking one instead of claiming
  absence.
- **One status line, one Start now.** The Skales Local card rendered two
  answers to "is it running", one of them with a dead start button when no
  model was installed. One resolver decides, and the more concrete answer
  wins.
- **Connecting a Google account survives its own landing page.** The return
  page re-ran its effect the moment translations finished loading and burned
  the one-time code, which read as "This authorization is no longer open"
  seconds after a successful consent. With remote access on, the final step
  was rejected as unauthenticated and surfaced as a bare "Failed to fetch".
  The landing step is reachable on its own now, the code is claimed before
  the exchange, a run lives an hour, and a second tab can no longer erase
  the account the first one connected.
- **A tool that is named can be called.** The capability answer listed tools
  from the catalog that the running turn had not loaded, and the model,
  unable to find them, tried them as shell commands and invented an excuse
  when that failed. Naming now loads, a tool name typed as a shell command is
  steered back to the tool instead of executed, and the answer says how a
  not-yet-loaded tool becomes callable.
- **Credentials stay out of transcripts.** The integrations folder is sealed
  against file tools the way settings.json already was, and web requests
  redact tokens and secret fields by name in what they log and store.
- **Calendar reads with your account, not just your key.** With both an API
  key and a connected account, events were fetched with the key alone, which
  can only see public calendars and read as "google rejected access". A
  rejected fetch retries once with the account token, and writes always use
  credentials that can write.
- **Saved keys stop eating themselves.** Twenty fields showed a shortened
  key prefix after saving, and saving again wrote that shortening over the
  real credential, after which every check failed. A saved secret shows as
  dots that are never written back.
- **Replies to bug reports arrive.** An answer from the maintainer was
  fetched and thrown away by the notification poller, unclickable on the
  notifications page and invisible on the feedback page. It now surfaces in
  all three places.
- **Searching Settings finds every line again.** The 12.7.4 rule that let a
  section-name match win outright also erased every inventory match, which
  made "Google" unable to find the Google account setup inside Productivity
  Integrations, and the same for 144 other word-to-section pairs. Name
  matches sort first now, everything else follows under a divider, and a
  test probes every word of every section name and keyword against the index.
- **A turn that ends with nothing says what ended it.** A dropped connection,
  a filtered answer, an output limit reached before the first word, a reply
  spent entirely on reasoning and a genuinely empty answer all ended in the
  same sentence with no log line, and the retry button only existed in
  English. Each cause has its own sentence in all twelve languages, one log
  line names it on the server, and the reason and retry survive a reload.
- **Post generation stops calling a token limit a timeout.** Discover's
  compose gave every model 120 tokens; reasoning models spent them thinking
  and never wrote, and the empty result was labelled "your model did not
  answer in time". The budget starts at 1024 and climbs before reasoning is
  switched off, an answer cut at the limit is named as cut, and a fallback
  template says why it is one.
- **The diagnosis stops blaming the outside for a stop Skales ordered.** The
  server exit recorded as an external SIGKILL during Discover traffic was
  Skales's own five-second shutdown grace ending a long request; measured,
  not guessed. The forced stop marks itself and the diagnosis names it. The
  same report also claimed crash recording was off while it was on; the
  answer now comes from the installed handlers instead of an env guess.

## v12.7.4 - Skales Local

A release about screens that tell the truth. A connection that worked but read
as broken, a tool that was announced and could not be called, a question asked
by the operating system in a way that left the window unusable afterwards. Five
copies of the same answer do not make it true, so there is one of each now.

### Added

- **A Telegram bot in a group can be told when to speak.** It answered every
  message in the room, which is fine where the room is built around it and
  unusable where people are talking to each other. Settings, Integrations,
  Telegram now offers every message, only when mentioned or replied to, only
  replies, or only commands. Every message stays the default, so nothing changes
  for a bot that works today. Any single chat can be muted without unpairing it,
  and a group that pairs from now on can start muted and is told so when it does.

- **Skales ships its own server.** Skales Local is a provider like any other
  in the list, with one difference: it asks for no key, no account and no
  install. The inference server lives inside the app. A new Settings tab
  carries the models, with search, categories (language, seeing, voice, image,
  imported, downloaded) and sorting by size, name or last used.
- **Four capabilities, four separate decisions.** Chat, dictation, read-aloud
  and images can each be set to this machine first, cloud first, or off.
  Images local, voice local and the language model through OpenRouter is an
  intended combination, and so is every other one, on every surface that
  needs a model.
- **Whoever answers is named, before and after.** The model line above the
  composer reads "Skales Local" and the model with it, so the choice can be
  checked before a word is sent. Set a capability to this machine first and it
  is never quietly pulled into the cloud. When a turn does have to leave the
  machine, the answer carries the reason; when neither side can answer, there
  is a sentence and a next step instead of a spinner.
- **Fetch a model, bring your own, or adopt one.** The download shows real
  progress, resumes where it stopped, and checks the file against its checksum
  before it counts as installed. A repository that wants a licence confirmed
  first says exactly that instead of "download failed". Your own .gguf, .onnx
  or .safetensors files come in through the file picker, and existing Ollama
  and LM Studio folders are recognised and used where they lie: the disk pays
  once. A file that is part of a model rather than a model - a projector, an
  autoencoder, a text encoder - is recognised as one and shown as one, so it
  never turns up in a list of models to answer with.
- **Dictate and listen with no network.** Whisper for hearing, VITS voices for
  speaking, both through one runtime and one install shared by Chat, Iris and
  AIPointer. Ten of the twelve interface languages have a voice, Croatian
  among them. Japanese and Korean have none we are allowed to ship, and the
  screen says so.
- **Pictures that never leave the machine.** The local image model sits behind
  the same tool as before, in Chat, in Studio and in Flow, as a backend and
  not as an island of its own. Skales reads the graphics chip's report first:
  if it cannot run the needed kernels, the picture runs on the processor,
  named, instead of crashing.
- **Every licence is in the app.** Settings, Advanced, Third-party notices:
  the open-source components and the terms of every local model, full texts,
  offline and searchable. A build that is handed a catalog model without its
  licence does not pass.

- **The sidebar becomes the chat's own navigation.** In a conversation, on the
  themes with a full sidebar, it shows New, Agents, Tasks and Add-Ons & Skills
  as overlays over the same screens the routes show, and below them the history,
  grouped by day, pinned chats first. A row says what its chat is doing -
  running, waiting for an approval, failed, a goal met - and keeps saying it
  after a reload, because the state is written to disk. A chat that came in over
  WhatsApp, Telegram, the phone or a task wears that channel's mark; everything
  else wears a quiet bullet. Hold still on a row and it gives you the date and
  time; pass over it on the way somewhere else and it stays out of your way.
  Home and Work stay on top and are the way back.
- **Setup names the three ways in.** Skales IQ, Skales Local with what it
  costs on disk and its Beta label, or your own key.

### Fixed

- **One dialog asks every question.** Deleting a chat on Windows put up the
  operating system's own box, and after it closed the composer was dead until
  you switched windows and back. Sixty-four of those calls across the app are
  now the Skales dialog, in every window, and the keyboard goes back to the
  button you pressed.
- **A ChatGPT subscription reads as connected, because it is.** The status card
  asked one question and the chat asked another, so a paid account that answered
  every message was reported as no provider at all. GLM and Qwen were read and
  never mentioned. There is one predicate now, and a new provider card lists
  itself.
- **Skales stops calling twelve of its own abilities switched off.** The
  self-report looked them up under a name the settings page does not write, so
  the answer was silently no. Places, Planner, Teams, Extensions and eight more
  now report the switch they actually have.
- **Drive, Docs, Calendar and YouTube can be called when they are configured.**
  They were announced as active and then were not in the toolbox, because the
  announcement and the catalogue read different files. Calendar was the same
  mistake one step worse: a machine with only an Apple or Outlook calendar had
  no calendar tool at all.
- **Connecting a Google account no longer paints an error over the success.**
  The landing page finished the exchange a second time and reported that the
  authorisation was closed, on top of a connection that had just worked.
  Disconnecting it has an exit too: a request that is dropped rather than
  refused used to leave the card waiting for ever, and a delete that failed
  still said the account was gone.
- **GigaChat can use tools on both of its hosts.** The current one refused the
  follow-up request because a tool result was sent as raw markdown where it
  wants JSON. The older one has no tools field at all, so the tools travelled
  there and were discarded before the model saw them; it now gets the contract
  it publishes. Model lists belong to the host that offers them, so the endpoint
  you selected stops offering a model it answers 404 for.
- **A tool call written in any script is recognised as a tool call.** The
  recogniser was a list of known markers, and a list only grows after somebody
  reports what it missed. It asks a structural question now, so a block in
  Arabic or Cyrillic is caught the same as one in English, and a fenced example
  is still left alone.
- **The Google Places hint names the API that is actually called**, which is
  Places API (New) and not the older one with almost the same name, and the card
  has a test button that hands Google's own answer back word for word.
- **Pressing Try again after a crash reloads instead of remounting.** The
  retry rebuilt the router inside a document that had already lost it, so
  /profiles and /settings answered a crash with the same crash, three times in
  three seconds.
- **Notifications stop appearing under the window buttons** on Windows, and the
  full-screen close button in Studio moves out from under the system one.
- **A tool that cannot work stops being asked again.** A missing browser, a
  search that hit a bot challenge, a path that is a folder: those were retried
  five and nine times a second with slightly different arguments. The failure
  now says whether it is worth another try, and the runner listens.
- **A tool name mangled by the model resolves to the tool it meant**, instead of
  being reported as an unknown one.

- **A local model could end up in the cloud without anyone saying so.** Four
  places outside the chat path ended their provider chain at a paid service
  when they did not know the configured provider: Autopilot, Lio and two of
  the background runners. An unknown provider is a named error now, not a
  silent detour, and the hard-coded paid default is gone.
- **Agents, Flow, Studio and Lio ask the same place the chat asks.** All four
  used to decide on their own which provider answers, and Flow's fall-back to
  a cloud-only list happened in silence. They go through the same resolution
  as everything else now, and Studio's Auto names the model that actually
  answers.
- **A downloaded model is also found.** The downloader wrote into a folder the
  server never looked at. One folder for both.
- **The machine may sleep.** A generation that runs into system sleep is
  called sleeping now instead of nothing; on waking, Skales probes the server
  and only restarts it when it has really lost it.

- **The chat page outlives a long answer.** After minutes of an unanswered
  turn the page could be replaced by a crash card, React's error 482. The page
  was suspending its own router: every timer on it polled through server
  actions, and each of those parks the router on an unresolved promise. The
  timers poll over plain requests now, and the seven-minute watch that used to
  die in the fifth minute runs through with the honest timeout line still on
  screen.
- **A Telegram answer belongs to the chat that asked for it.** When two chats
  wrote at the same time, one could be handed the other's answer, pictures and
  even an approval keyboard included. The answer is picked by chat now, and
  the bot refuses to post one addressed to another room.
- **Iris's mute button leaves the window buttons alone**, and muting her means
  she stops listening, not that she stops talking: the microphone closes, the
  sentence finishes, and the choice survives a restart.
- **Opening Iris no longer announces anything.** She does not greet you, she
  listens. Same as on the phone: the window opens, the ear is ready, and the
  first sentence in the room is yours.
- **The floating Save button no longer covers the end of a settings tab.**
- **The size on a model card is what the download really costs.** A model that
  can see is fetched together with the file it sees through, and the card
  counts both. The description sits behind the card's own chevron, so the
  list stays a list.

### Changed

- **An image that goes to another model says so.** The routing was right and
  invisible: a picture handed to a model that can see it was a console line and
  nothing on screen. The conversation now carries a line naming the model, and
  says explicitly when the image left the machine.
- **A pairing that fails against an old desktop says which versions are
  involved**, on the phone, instead of fifteen silent seconds. The confirmation
  dialog on the desktop shows the phone's version too, which is the one dialog
  where the owner had no way of seeing what was asking.
- **The Agents page asks before deleting the way everything else does.** Its
  second-click-within-four-seconds pattern was built to avoid the operating
  system dialogs that this release removed.

## v12.7.3 - Drop-In

A release about being answered. A local provider that would not talk, tools
that reported more than they did, and a call you can now interrupt in the
middle of a word.

### Fixed

- **LM Studio answers again.** The provider card said no API key was needed and
  every message came back asking for one. The guard in front of the chat kept
  its own list of the providers that need none, and had never been told about
  LM Studio. That list is now written once and asked from everywhere.
- **The LM Studio card's Refresh Models button exists.** Its own hint told you
  to press it; the button was never drawn, and the model list behind it was
  refused for want of a key it never needed. A running LM Studio now shows up
  in the model picker, in model search, on the Agents page and in the provider
  grid, and an agent pinned to it stays pinned.
- **A group chat can be pointed at your own OpenAI-compatible endpoint**, and
  the group-chat skill runs on a local model with no key at all.
- **A failed Windows update leaves a trail.** Every step of the update path is
  written down with a timestamp, and the last lines of it ride along in
  Settings > Advanced > Diagnostics. The app also stops quitting on a timer
  while the installer is still starting: it waits for the installer to actually
  be there, and says so when it is not.
- **A failed send is explained the way a failed test is:** the mailbox that
  refuses your password says which credential it wants and where to get it, on
  every mail action rather than only when you press Test.
- **A mailbox that cannot be opened no longer answers under the name you asked
  for**, and a list that was shortened says that it was shortened. Emptying the
  trash reports what it deleted, not what it found.
- **A calendar that could not be read says so** instead of shortening your week,
  and a week with no calendar connected no longer reads as a free one.
- **Speech, images and video stop claiming more than they did.** A voice that
  fell through to a free fallback says which provider refused and why; text that
  was too long to speak says it was cut; a video job that failed says it failed
  instead of going quiet after "started"; and a provider that answers 401, 402 or
  429 is named as your account rather than as a fault in the app.
- **There was a second copy of the speech cascade**, and the two had drifted far
  enough that the same settings produced two different voices. There is one now.
- **A file too large for the phone is refused before the connection breaks.**
  The ceiling was set by an estimate that counted one round of encoding; it is
  now the measured number, written down once for both file paths.

### Changed

- **Talking over Call Mode interrupts her mid-word.** Call Mode used to stop the
  audio only after the sentence had finished. It now listens through playback
  with the same calibrated listener the Iris window uses, and hands the turn
  back to you as soon as you start speaking. Where the microphone cannot be
  opened for it, the old behaviour stays and the screen says why.
- **Iris uses your name the way a person does:** once at the start of a
  conversation, and after that only when she is actually addressing you.
- **Qwen 3.7 gets a profile of its own:** the thinking-mode sampling the vendor
  publishes instead of the generic Qwen profile's non-thinking numbers, and a
  long-thinking budget so a silent stretch is not cut off as a stalled stream.
- **The guide has its first screenshots**, and the LM Studio and KoboldCpp setup
  pages have caught up with the provider grid that replaced the old dropdown.
- **The acknowledgment that fills the silence follows where your endpoint
  actually points**, not what it is labelled. A custom endpoint on the internet
  is treated as being on the internet; one that cannot be read at all stays
  silent, as before.

## v12.7.2 - Cockpit

A release about seeing what is going on. What is running and where, what a
conversation is costing, what a tool actually did or did not do, and what the
model wrote when it meant to call one.

### Added

- **Autopilot opens on what is in flight.** Goals, runs, tasks and scheduled
  jobs stand in one list, each as a single line saying what it is, which
  surface it is on, which model is answering, when it last moved, and the one
  thing you can do with it. Code sessions working in the background appear
  there too; no screen showed them before.
- **A Code session can carry a goal.** `/goal` in the Code window arms the same
  ledger the chat uses, and a strip above the composer shows what the session is
  working towards, how far it has got, and whether it is running, waiting for
  you or on hold. It survives a restart, so a coding task can be followed over
  days rather than a sitting.
- **Sessions have addresses.** A model can list your other conversations - title,
  surface, model, open goal - and leave an unread note in one of them. It starts
  nothing over there and spends nothing. An isolated agent can do neither, and
  the refusal sits in the tool itself rather than in whoever called it.
- **A long conversation offers to carry on somewhere fresh.** When a conversation
  has really grown - several exchanges, and the size spread across them rather
  than sitting in one enormous turn - the chat says how much history it is
  carrying and what a new session with a summary would start at. It only ever
  offers. Nothing is deleted, and nothing is spent without you saying so.
- **An available update stays visible until you deal with it.** A pill in the
  window chrome, fed by both the updater and an announcement from us, instead of
  a toast you had to be looking at for five seconds. Hiding it is remembered
  until the next version, and it goes away by itself once the update is in.

- **WordPress can do what your site can do.** Skales reached about a third of a
  WordPress site before. Now: find, read, change and delete posts, the media
  library, categories and tags, comment moderation, menus and widgets, colours
  and fonts, the logo, site settings and permalinks.
- **The featured image finally gets set.** "Write a blog post and add a fitting
  image" used to put the picture in the body only, and the thumbnail your theme,
  your post list and every WhatsApp or LinkedIn preview shows stayed empty.
- **The phone reaches the cockpit.** The session directory, everything in
  flight and how to stop it, a coding session's goals, the handover, and
  leaving a note in another session. It can also open a coding session's file
  tree and read a file out of it.
- **Something is said while the model is still thinking.** A big model can sit
  silent for several seconds before it writes anything, and a pulsing dot in
  that window reads as broken. One short line now says what is about to happen,
  in your language, and disappears the moment the real answer starts. It is
  never stored and never sent back to a model on a later turn.
  Who writes that line follows where your conversation already goes: on Skales
  IQ it comes from Skales and says so; on your own key it comes from a small
  cheap model at the same provider; and if you run a local model it stays on
  your machine or does not happen at all. One switch, in Settings under
  Chat & Code, Assist, turns it off everywhere.
- **A team run shows up in the list of what is going on.** A team of agents
  working through a task was the loudest thing in the product that appeared on
  only one screen. It has a line now, with the stop button the other rows have.
- **Sub-agents can be given a role.** A dispatched sub-agent inherits every tool
  in the catalogue and pays for the whole list on every turn. Eleven roles have
  existed for a while with no way to reach them; there is a setting for them
  now, and it still starts on the old behaviour.
- **The cockpit says when a hand-off is worth taking.** The phone can see it
  too: how much history a conversation carries into every turn, and what a fresh
  session on a summary would save.

### Fixed

- **The nightly identity upkeep stops looking in the wrong place.** The built-in
  3am job that keeps what Skales knows about you up to date was pointed at its
  memory folder by a relative path, and every night it landed in the program
  directory instead: two red lines in the log, and the half of the job that
  reads your recent memories never read anything. It names its folders outright
  now. The same slip was in the file tools underneath it - listing or creating a
  folder by a short name went to the program directory while every other file
  tool went to your workspace - so they agree now too.
- **Skales answers questions about itself from its own manual.** Asked what a
  setting does, it used to go rummaging through its own program code: thirteen
  tool calls and a very expensive turn for an answer three sentences long, and
  on a normal installation that code is not even there. It reads the guide, the
  changelog and its capability list, and if the answer is in none of them it
  says so rather than going digging.
- **The invitation to send feedback waits until you have actually used the
  desktop app.** It only asked how old the installation was, so somebody who
  installed Skales on their computer, worked on their phone and never opened the
  desktop window got a form three days later asking what was missing.

- **Mute now means mute.** Switching notifications off silenced everything
  except the messages Skales considered important, which is how two reminders
  arrived at ten to midnight with the switch on. Nothing goes out on any channel
  now, and nothing is swallowed either: what was held back waits on the
  Notifications page, and a line at the top says how many important ones there
  were.
- **One meeting, one reminder.** Three parts of Skales reminded about calendar
  events without knowing about each other, so a single entry could ping you
  twice. Whichever gets there first now owns the reminder for that event that
  day, and tomorrow's occurrence of a recurring entry reminds again as before.
- **Friend Mode switched off also stops Skales taking the initiative.** The
  proactive assistant had its own switch buried on the Notifications page, so
  turning the companion off left it running and it kept surfacing meetings and
  overdue tasks.
- **An approval request obeys the mute too.** It went straight to Telegram and
  never asked, so it was the one ping a muted user still got.
- **Unchecking every notification channel now actually means silence.** A
  last-resort system notification still went out, because it could not tell
  "the channel you picked is unreachable" from "you picked none". The first one
  still gets the fallback; the second one gets the Notifications page.
- **A tool call the model wrote as text no longer becomes the answer.** In any of
  the shapes models write it in, including the ones that are not in English and
  the ones that look like a line of Python. A call whose name just missed a real
  tool is now corrected in the conversation instead of being dropped in silence,
  and the same correction reaches Telegram, WhatsApp and the CLI, not only the
  desktop window.
- **Personas report a failed tool as a failed tool.** Inventing a plausible
  result was the most expensive thing any of them did.
- **A provider error says which provider, which status, and what the provider
  itself said** - in every language. The detail used to be attached to the
  English sentence and then thrown away by the translation.
- **A refused page stops being reported as a page.** `fetch_web_page` and
  `extract_web_text` lead with the HTTP status and say in words when what came
  back is a server's error page. A PDF, an image or an archive is refused by
  name instead of being run through the HTML strip and handed over as text.
- **Web search says whose problem a failure is.** A rejected key is a rejected
  key, a spent quota is a quota, and a provider having a bad day is that. The
  SearXNG hint about JSON output only appears on the status that actually means
  it.
- **A file tool says which argument it is missing,** and accepts the argument
  names models really send. A well-formed call carrying `contents` or
  `file_text` used to fail where a malformed one carrying the same key was
  repaired.
- **A write that wrote nothing leaves nothing behind.** No folders, no backup
  file, and an existing file untouched.
- **A full disk reads as a full disk** in every file tool, not as an errno line
  in five of them.
- **A signed-in ChatGPT, Claude or Gemini subscription is stored the way an API
  key is stored.** The token that signs you in sat in the settings file as
  readable text next to keys that were encrypted; it is encrypted now, and an
  existing sign-in is converted the next time Skales starts.
- **A schedule that switches itself off says so.** After three failed runs a
  schedule pauses itself, which was right - but it only wrote a log line, so a
  nightly job could simply stop happening and nobody found out.
- **The phone stops waiting for an answer that was never coming.** Two of the
  requests it makes about a coding session could end without a reply at all, so
  the phone sat out its timeout and then blamed the computer instead of naming
  the reason.
- **Listing a folder no longer empties the context window.** The listing is
  capped, says how many entries there are in total, and no longer holds up every
  other conversation while it counts.
- **A big image no longer breaks the automation.** A generated picture used to
  travel through the model as one enormous block of text before it was uploaded;
  Skales reads the file itself now. That is where a scheduled overnight run used
  to stop.
- **SEO is read before it is overwritten.** And when the site is set to
  discourage search engines, Skales says so instead of reporting SEO nobody will
  see.
- **A refusal names what is missing.** If the connector hangs on an account with
  limited rights, Skales says which right is missing on which account. And when
  a design change would do nothing in the active theme, it says that instead of
  reporting success.
- **A signed-in ChatGPT subscription starts on the general flagship** instead of
  the Codex coding model, and the account's own model catalogue is asked for
  rather than assumed.
- **Google says why it refused:** a location it does not serve, a rejected key,
  an API that was never switched on, or an exhausted quota, instead of one
  blanket "check your settings" for all four. The same refusal from OpenAI and
  from a ChatGPT subscription reads the same way, in every language.
- **The chat model picker lists the active provider's models straight away**
  instead of waiting for a search term, and an install with no provider set up
  is told that, rather than pointed at a search box with nothing behind it.
- **The CLI routes that manage MCP servers ask who is calling.** Four of them
  did not: one handed out the stored server configuration including the linked
  server's keys, one deleted a definition, and two started and stopped the
  configured process, for anyone who could reach the port. The DevKit token is
  now compared the same careful way as the API token beside it.
- **A command that fails says how it failed.** The exit status travels with the
  answer, so a search that found nothing reads as nothing found rather than as a
  broken command, and a program that is not installed says that instead of
  blaming the command. Output too large to read back is reported as a command
  that ran with its output cut, not as one that never happened.
- **A shell tool with nothing to run says which argument is missing** instead of
  answering with an internal error, and the same goes for the browser: no
  address, no target to click, no key to press. A scroll direction the browser
  does not have is refused rather than quietly scrolling the other way.
- **A page that was not there stops being read as a page.** When a site answers
  404, 403 or 503, the browser says so before the error page's own words are
  handed over, the same way fetch already does.
- **Background commands left running by a previous start are visible again.**
  After a hard restart the list said nothing was running while last session's dev
  server still held its port.
- **Twenty translations had lost the value they were supposed to show** - a count
  with no number, a countdown with no seconds. They are repaired, and a build
  check now catches the next one.
- **The profiles that ship inside Skales say the same thing as the public ones
  again.** Eight model families, GigaChat among them, existed only in the
  library fetched over the network, which means exactly the machines that cannot
  reach it were the ones without them.
- **The GigaChat card offers the generation it can actually reach** and no longer
  asks for a certificate that ships with Skales.
- **The release check stopped warning about two Windows manifests** that were
  never finished and have now been removed.

### Changed

- **The user guide is a guide again.** It had a chapter system and no way to use
  it: the chapter headings in the sidebar were not links, and there was no
  search at all. The sidebar now switches chapters from anywhere, the search
  reaches every chapter at once, and each of the eleven chapters opens with a
  walkthrough you can follow on your own installation in about three minutes.
  Mobile and remote, which had no chapter, has one; so do the model readout, the
  profiles page and updates. What was in the guide and no longer true went out
  with it.
- **The waiting line, the model that writes it and its fallback stand together
  in one place.** Three settings that explain each other sat under Goals, where
  the first of them has nothing to do with goal mode, and the same feature was
  described three times on one screen. They are one section now, Assist, in
  Settings under Chat & Code, with one explanation, and the fallback sits
  underneath the switch it belongs to instead of beside it. Nothing you had set
  changes; the guide has a section on it.
- Update announcements reach every screen in the app, not just the dashboard.

## v12.7.1 - Which Model

Two reports on the day 12.7.0 shipped, both about the same thing from opposite
ends: a model you cannot reach, and a model the app names wrongly. This patch
answers both.

### Fixed

- **GigaChat reaches its current generation.** Skales talked to Sber at the
  older address, which does not serve the GigaChat 3 models at all, so an
  account with a live Ultra entitlement could not reach a single one of them.
  Skales now uses the current address, and the GigaChat card has an endpoint
  field with both hosts on it, so a company still working against the older one
  can stay there and anyone running their own deployment can type its address.
- **A model id you type is sent, not second-guessed.** Typing a model name that
  Skales had never heard of came back as "the model was most likely retired or
  renamed" even when the model was alive and well and the real problem was the
  address it was asked at. Skales now only says a model is gone when the
  provider says so; anything else names what actually happened and points at the
  setting that causes it.
- **The context readout names the model that is answering.** Picking a model for
  one conversation changed what answered but not what the readout under the
  composer said: it kept naming your default model and measuring against that
  model's window. It now names the model the next message will use, whether that
  comes from the picker, from an agent, or from your defaults.
- **The status pill says where this conversation goes.** A chat running on a
  cloud model under a local default read "Local AI", and a chat running on your
  own machine under a cloud default was labelled with the cloud provider. That
  line is a statement about privacy, so it now follows the conversation in front
  of you and goes back to your default when you leave it.
- **Switching the model with a command changes this chat, not your settings.**
  Typing the switch command wrote the model into your provider card instead, so
  it could pair a model with a provider that does not serve it, it outlived the
  chat it was typed in, and since the conversation started carrying its own
  choice it no longer reached the next message at all while still reporting
  success. It is now the same per-chat switch the picker is: your defaults are
  left alone, the provider is worked out from the model, and if more than one of
  your providers serves that model, Skales asks which one instead of guessing.
- **Context windows come from the provider's own catalogue.** A model with a
  one-million window was measured as if it held 128,000, so the automatic
  shortening of long conversations began at roughly an eighth of the real
  budget. The window now comes from the same catalogue Skales already downloads,
  and both the readout and the shortening use it.
- **The Code window names the model that will answer.** A session with its own
  model was labelled with your default one, and a session with no model of its
  own showed nothing at all rather than the default it actually runs on. Its
  context figure was measured against a model nobody had selected.
- **Lio says what "use default" means.** The option said "Settings" while Lio's
  own saved configuration is what decides, so it could name a provider Settings
  had nothing to do with. It now shows the provider and model that will build.
- **Compacting a conversation by hand uses the conversation's model.** The
  button sized the history against a model that was not running, which on a
  large window meant it cut far more than it needed to.
- **A lost connection is no longer reported as a crash.** Looking at Skales from
  your phone or a second computer and the machine running it goes to sleep,
  restarts or changes network: the request never arrives, and the screen said
  "something crashed" with the browser's own words underneath. It now says the
  connection was lost, in your language, and explains where to look. Nothing had
  crashed in that case, which is why those reports were impossible to act on.
- **The diagnostics report stops calling ordinary shutdowns crashes.** Closing
  Skales stops its engine, and each of those was listed as a recorded crash, so
  a report of four crashes could be one crash and three normal exits. They are
  listed separately now, by name.
- **A recorded engine stop finally says what happened to it.** The report
  printed the word "undefined" for exactly the entries that carry the most: the
  signal, the exit code, the last output of the engine, and the last things the
  window asked for before it went away. All of it was recorded and none of it
  was shown.
- **Times in the diagnostics report are your times.** They were written in UTC
  and formatted to look local, so an event at 19:52 was reported as 17:52. The
  report now says which timezone it is using.

### Added

- **A bug report can carry the crash your computer recorded.** Reports that say
  "it closes sometimes" are impossible to act on, while the answer sits on the
  reporter's own machine behind a Copy button on another page. The report dialog
  now offers the last recorded crash as an attachment: switched off unless you
  turn it on, and the exact text is on screen before anything is sent.
- **The wait before the first word says what it is.** Between sending and the
  first character the chat showed a spinner and the word "Thinking", which is
  the same picture a hung request draws. It now says it is reading your prompt
  and how long it has been doing so, and on a model running on your own machine
  it adds that this phase is silent by nature and can take a while on slower
  hardware. The moment the first word arrives, the line is gone.
- **GigaChat works without hunting for a certificate.** Sber's endpoints are
  signed under the Russian national root, which no operating system carries, so
  until now the first step was finding that certificate and pasting it in.
  Skales ships it and uses it for GigaChat requests only. It is never installed
  on your computer, never applied to any other provider, and it is added to the
  roots your system already trusts rather than replacing them. The field stays
  for anyone who needs their own certificate, and the card shows the exact file
  the bundled one came from.

## v12.7.0 - Everybody's AI

Skales started as a platform for everyone: set up in minutes, no prior
knowledge, grandma approved. Over the past two months Skales has grown fast -
and week by week it became more and more a power user's tool. Both are true,
and both belong to Skales. But one promise was at stake: the lightness.

So with this release we rethought setup, add-ons and settings. On first start
you choose what you want Skales to do for you - and that is exactly what you
get: a sidebar that only shows what you use, settings that in Standard show
only what belongs to your choice. And for everyone who wants everything:
Advanced still shows every screw, same as always.

Every single piece of feedback was read. We matched the roadmap against your
feedback and pulled forward what helps you today. And the promise stands: we
will keep helping people who do not know what an API is to run a safe, local
agent on their own computer - one that respects your privacy and ties you to
no cloud and no provider.

Skales stands for everybody's AI. What it is not yet, it will become.
We do not build for a target group - we build for every one of you.

### Fixed

- **A local model is allowed to think before it answers.** LM Studio, llama.cpp
  and anything else on your own machine sends nothing at all while it reads your
  prompt, and on slower hardware that quiet stretch runs for minutes. Skales
  measured it with the same thirty-second stopwatch it uses for a cloud stream,
  declared the connection dead, silently sent the whole request a second time
  (so the machine evaluated the same prompt twice), and then reported that the
  provider had stopped responding. Local and custom endpoints now get a
  generous window for the wait before the first token, and the ordinary short
  window from the first token onward, so a socket that really dies mid-answer is
  still caught. The Request Timeout slider in Settings raises that first window
  too; until now it only ever governed the wait for the connection itself, which
  is why turning it up changed nothing. Nothing about cloud streaming changed.
- **A local runtime is no longer asked to do the work twice.** A stalled stream
  used to be retried by sending the identical request again. Against a cloud
  endpoint that is a cheap second try; against your own machine it throws away
  the prompt evaluation already done and starts it over, so the retry is slower
  than the attempt it replaced. Local endpoints now report what happened instead.
- **An interrupted answer says it was interrupted.** When a provider's
  connection closes mid-generation without saying why, Skales used to record
  that as a normal finish. At the end of a chain of tool calls the result was a
  cut-off turn presented as a completed one, sometimes with nothing but a
  cheerful closing line. A dropped connection is now named as one: an answer
  that was already partly written is continued, an empty one is reported and
  retried rather than accepted, and the same is true on the Anthropic and Gemini
  paths, in the chat, and in the Telegram and WhatsApp loops.
- **A model that thinks slowly can now say so.** Skales decides how long a model
  may stay silent by reading its name, which works until a model that thinks for
  minutes is called something that mentions none of it. An LLM profile can now
  declare it outright, so the next model of that kind is a profile update rather
  than an app release. Nemotron is declared in the built-in profiles.
- **A blocked free model no longer sends you looking for a replacement.** When a
  provider answers that no endpoint matches your data policy, Skales used to say
  the model had most likely been retired or renamed. The model is there; your
  account's privacy settings exclude every endpoint that could serve it, which
  is what usually happens with free variants. The message now says that, and
  names the model.
- **A 403 from OpenRouter names its three causes.** The key being disabled or
  over its limit, the account privacy settings excluding every endpoint for that
  model, and the model's provider not accepting requests from your location, in
  the order worth checking.
- **Provider errors are read in your language.** Every provider error already
  carried a translation key and nothing ever looked one up, so all twelve
  languages saw the English sentence. They no longer do.
- **Excel and Word files can be attached.** Skales could read a .xlsx or a
  .docx off disk for a year; the paperclip just did not know the formats
  existed, so the picker never offered them and a dropped file came back with
  "cannot be read directly" and instructions to save it into the Workspace by
  hand. They attach now, in the chat, on the new-chat screen, in a coding
  session and from the phone, through the same readers the agent already used.
  A format nothing here can open (.doc, .ppt, .rtf) now says which one it is and
  what to export it as, instead of getting the same sentence as every unknown
  binary.
- **The conversation says when it stops carrying everything.** A long
  tool-heavy chat was cut to its last sixty entries at a fixed count, silently,
  while the context meter still showed room: the cut counted messages and the
  meter counted tokens, so a model started forgetting for reasons nobody could
  see. The limit now comes from the model's own window, older parts are
  summarised rather than dropped wherever that is possible, and either way a
  line appears in the transcript saying what happened. Nothing was ever deleted
  from disk, and the line says that too. Reopening a long chat shows the same
  notice in all three places it can happen; before, only one of them mentioned
  it.
- **Windows window buttons stop sitting on the Code window's header.**
  Minimize, Maximize and Close are painted over the page on Windows, and the
  Code window's header put the model chip and its buttons in exactly that
  corner. The header now reserves the width the system reports for those
  buttons, so it stays correct at any display scale. macOS and Linux are
  unchanged, and the other four windows were checked: two already reserve the
  strip, and two have no system buttons at all.
- **A working agent is no longer stopped for being slow.** Sending a second
  message while Skales was answering the first one cancelled the answer, even
  when it was three tool calls into finishing the job. Now the count only
  builds while the turn produces nothing: an agent that is visibly working
  keeps working however impatient you get, and one that has gone quiet is still
  stopped after two messages. "stop" still stops it immediately.
- **Two people on one Telegram bot are two people.** Several Telegram accounts
  writing to the same bot shared one conversation, and every message was
  labelled with the name of whoever paired the bot first. Each sender now has
  their own conversation and their own name, and /clear clears their own rather
  than everyone's. WhatsApp already worked this way.

- **The Tasks page no longer crashes on a task with no name.** A to-do created
  by a model that left the title out was written to disk without one, and every
  later visit to the Tasks page hit the crash screen instead of the list, over
  and over, until the file was deleted by hand. A task that arrives without a
  title is now named after what it says to do, both when it is written and when
  an existing one is read, so the ones already sitting on your disk stop
  breaking the page.
- **Your own endpoint is no longer capped at 65K.** A custom or LM Studio
  endpoint had no per-model table to look its context window up in, so it fell
  to a generic 65,536 - and a 200K model was compacted at a third of its window.
  Three things changed. The endpoint gets to report its own window, and Skales
  reads it whatever the server calls it. The extra endpoint slots, which had no
  advanced panel at all, now have the same timeout, retry and limit controls
  every other provider card has. And a number you type into Override Model
  Limits now wins over everything else, which it did not: a window reported by
  the endpoint quietly overruled it. The panel also says which of the three the
  figure came from, so a setting that took can be seen to have taken.
- **The context meter shows what Skales is actually using.** It resolved the
  window without ever looking at your overrides, so raising a limit in Settings
  changed how much Skales sent and never changed the number on the screen, which
  is how anyone checks. It reads the same override the call sites read now.
- **The empty composer is one line tall again.** The box grows with what you
  type by measuring itself, and the measurement counted the placeholder text as
  if it were a draft. Any moment the window was narrow - a page transition, a
  resize - "Ask anything, or describe a goal..." wrapped to six lines and a
  six-line box was burned in, and it stayed that way, because the height was
  only re-measured when the draft changed and an empty field has no draft. The
  thinking dial and the access lock also stood half again too far apart, and now
  sit as one pair. Both the start screen and the chat.
- **The German in the app says "du" everywhere.** The WordPress card, the two
  subscription disclaimers and the whole AIPointer page addressed you as "Sie"
  while the rest of the app said "du" - a tone that switches mid-app reads as
  two products stitched together. Thirty-three strings pulled across, each one
  read first, because German writes the polite address and the ordinary third
  person with the same words.
- **The German in the app is written in German again.** The strings the new
  setup and the Standard view brought with them arrived with every accent
  stripped, in all seven Latin-script languages: "Was soll Skales koennen?",
  "Rien n'est definitif", "Skales nen lam duoc nhung gi?". That was the
  onboarding, the first screen a new user reads. All of them are restored, and
  the German file is now clean throughout - two hundred more strings had been
  carrying "aendern" and "Gedaechtnis" since earlier releases. A build check
  counts the accent-free sentences per language so the next batch cannot arrive
  the same way.
- **Two settings nobody could search for.** The safety levels answered to their
  old names - strict, balanced, permissive - so typing "unrestricted" into the
  settings search found nothing, and the folders Skales must never touch
  answered to nothing at all. Both are findable.
- **Simple chat stops offering DLNA casting.** It has been retired since
  v11.3.2 and was still being advertised as always available.
- **A crash that happens in the window is now recorded like one.** Until now
  only the background processes wrote to the crash log, so a user asked to
  reproduce a crash and open Settings, Advanced, Diagnostics found nothing
  there. A render crash now lands in the same place, with its stack, and the
  crash screen says where to find it. In the chat it also carries the component
  trail that names what threw, which is the one thing that makes a crash nobody
  can reproduce fixable.

### Added

- **GigaChat is a provider now, not a workaround.** Sber's models were reachable
  only by running a proxy of your own, because the custom endpoint field wants a
  key it can send as-is and GigaChat wants something else twice over: an
  Authorization Key that has to be traded for a token roughly every half hour,
  and a root certificate that is in no default certificate store. Skales does
  the trade itself, keeps the token fresh, and trusts that certificate for
  GigaChat's requests only - not for this computer, and not for anything else it
  talks to. Paste the key, pick whether it is a personal, B2B or corporate one,
  and add the certificate on the card.

- **You can put a chat on a shorter leash.** Next to the thinking dial in the
  composer there is now an access control: read-only, read and write, accept
  edits, or auto. Read-only means Skales can look at anything and change
  nothing, and it is refused in the place tools actually run, not merely greyed
  out on screen. It is set per conversation, it survives a reload, it is there
  on the start screen as well as in a running chat, and read and write, exactly
  what Skales did before, stays the default. When a folder is bound in Code mode
  as well, the shorter of the two leashes is the one that counts.
- **Skales can show you its own crashes.** Settings, Advanced, Diagnostics:
  what this machine has recorded, on screen and on the clipboard. Crashes, the
  provider calls that failed this session, recent warnings, and the version and
  hardware they happened on. It is read from this computer and sent nowhere. An
  install with nothing recorded says exactly that, rather than implying health.
- **The assistant reads its own logs before it explains a failure.** Asked why
  something broke, it used to answer from general knowledge about programs like
  itself. It now reads the same diagnostics you can see, and says plainly when
  they show no cause.
- **LM Studio is a provider with a name.** It always worked through the generic
  custom endpoint, for anyone who knew the port, the path and that the key
  field should stay empty. It has its own card now, with the address filled in,
  no key asked for, a note about the server switch that trips most people up,
  and the local prompt-evaluation window from the first item on this list
  applied without anyone configuring it.
- **Your own browser can do the browsing.** Settings, Browser Control takes the
  full path to a Chromium-based browser, so automation runs in the profile and
  the extensions you already have. Leave it empty and nothing changes. A path
  that is not there stops the launch and says so rather than quietly opening a
  different browser, and a fork or an old build that refuses to start is named
  as the untested thing it is.
- **The add-on cards stop taking each other's entries.** Two cards on the setup
  screen could claim the same add-on, so switching one off switched something
  else off with it. Each add-on now belongs to exactly one card, and the last
  screen of the setup says how many are on rather than guessing.
- **The accessibility check measures the two setup screens too.** They were the
  newest screens in the app and the only ones the audit did not open.
- **Eight accent presets, one click each.** The three-colour picker stays for
  anyone who wants it. Every preset is checked against the same readability bar
  the picker warns you about, in light and in dark, before it can ship.
- **The note that says what this release is about is in your language.** It sits
  above the changelog in Settings, Advanced, Updates, in all twelve - because the
  person who most needs to hear that setup and settings were rethought is the one
  who does not read release notes in English. It retires itself when the build
  moves off this line, so it can never end up describing an older release.

### Changed

- **The setup asks what you want Skales to do, and builds the app around the
  answer.** A new step at the end of the first run offers five kinds of work -
  Generative AI, Audio AI, Coding AI, Business agent, Personal agent - and each
  one switches on the add-ons that belong to it. Every individual switch stays
  on the same screen, so a bundle is a starting point and not a package. What
  gets stored is the add-ons, never the category. The sidebar is then built from
  that answer: an entry whose add-on is off is simply not in the list. Chat,
  memory, planner, tasks, schedule, history and Discover are not offered,
  because they are not a choice.
- **Settings has a Standard view and an Advanced one, and Advanced still shows
  everything.** The switch sits at the top of the page. Standard shows the
  settings your add-ons actually need; Advanced shows every setting there is,
  exactly as before. It is a view and not a state - anything switched on in
  Advanced stays on in Standard, only out of sight - and search reaches both, so
  typing what you are looking for finds it either way. A tab that would open
  onto an empty page in Standard is not offered. The Advanced view is not a list
  of dangerous things: Diagnostics, Updates, Export/Import and the data controls
  live in the Advanced *tab* and are shown in both views, because those are
  things ordinary users get sent to.
- **An existing Skales is offered the new setup once, and loses nothing to it.**
  A single notice, dismissable, that opens the same add-on screen with
  everything you already had left switched on. Settings, Advanced, Setup runs it
  again whenever you want. Nothing is deleted by either: chats, keys and
  settings stay exactly as they are, and only what is shown changes.
- **Who belongs to whom is decided in one file.** Which add-ons a category
  offers, which sidebar entries follow an add-on, and which settings sections
  the Standard view shows all used to be three separate lists kept in step by
  hand. They are one map now, and a test walks the real files to prove nothing
  is missing from it - an unplaced add-on or an unkeyed settings section fails
  the build rather than quietly becoming visible-to-everyone or
  invisible-to-everyone.
- **Skales knows the interface it is actually running.** The map the assistant
  answers "where do I find X" from is no longer written by hand: it is generated
  from the same files that build the sidebar, the Settings page and the Add-Ons
  list, and the build fails if the interface moves without it. So after a
  release that rearranged a great deal, asking Skales where something is gets
  the current answer rather than a confident description of the old app. The
  answer also says which view a setting is in and which add-on it follows.
- **A new installation starts with the Desktop Buddy switched on.** Buddy is the
  face of Skales - the skin is picked during setup and it is the most used thing
  in the app - and it was off unless you went looking for the switch. It now
  arrives on, introduces itself in your language the first time it appears, and
  points at Settings, General, where it is changed or switched off. An existing
  Skales is not touched: if you never turned Buddy on, it stays off, and if you
  turned it off, it stays off.
- **Discover and Memory now stand on both sides of the sidebar.** The feed and
  the working context used to live only in the Home view, so switching to Work
  took both away. They are core surfaces and follow you across the toggle.
- **Wrapped is always on again.** The yearly recap only works if its numbers
  are collected all year, so Wrapped is no longer an add-on you can switch off -
  which also means it returns for anyone who had it disabled. Its entry stays
  in the Home view only; nothing about what it collects has changed, and it
  still leaves your machine only if you share a card yourself.

## v12.6.51 - Name The Failure

A patch about failures that did not say their name. A browser that would not
start, a sign-in that stopped with three words, a settings page that took the
whole app down with it, and a picture that came back as an error nobody could
read. None of these were new. What was missing in every case was a sentence.

### Fixed

- **The settings page no longer takes Skales down with it.** On some Windows
  installs, opening Settings ended the engine outright: the page went blank or
  the window closed, Skales quietly restarted itself, and it happened again on
  the next visit. The cause was the browser automation library, which had moved
  on to a newer version than this build can run and stops the program the moment
  it is loaded. Skales now uses one exact version, checks before loading
  anything, and refuses to load something that would end the session. The
  "Install Chromium" button can no longer pull in a version that breaks the app
  it is meant to fix.
- **A browser that cannot start says why.** Missing Chromium, no system Chrome,
  a check the machine would not let Skales run: each of these is its own
  sentence now, in your language, with the two ways out named. Browser actions
  that fail can no longer end a reply in silence.
- **Signing in with ChatGPT explains itself.** "OAuth flow failed." is gone. The
  port being taken, a firewall refusing the local listener, a browser that would
  not open, a sign-in left too long: each says what happened and what to try, in
  your language. The port is checked before your browser opens, so you are not
  sent to a sign-in page whose answer could never arrive.
- **A window that dies is written down.** If a window or the engine stops
  unexpectedly, Skales records what happened and what it was doing at the time,
  and the window comes back with the reason on screen instead of vanishing.
- **Images reach local models again.** Pictures sent to a local model through
  chat, Telegram or WhatsApp could come back as an unreadable error, depending
  on where the picture came from. All three now prepare images the same way.
- **An older channel chat shows the model it is actually using.** Telegram and
  WhatsApp chats always followed your current model choice; the label just kept
  showing the one from the day the chat started. A model you picked for a
  specific chat is still yours and is left alone.
- **The model search finds an endpoint by the name you gave it.** Typing
  "LM Studio" found nothing, because the search only knew the internal id. Your
  endpoints now appear under their names everywhere, and the internal id is gone
  from the screen.
- **Local models get their own settings again.** A newer Gemma release was
  quietly running on settings meant for a much older one, and being handed a
  shortened prompt it did not need. Measured against the real model and
  corrected. The shared profile library and the copy built into Skales can no
  longer drift apart, which matters most where the library cannot be reached at
  all.

### Added

- **A "Free" filter in the model picker.** Shows only models nobody is billed
  for: free variants and anything running on your own machine. Worked out from
  the model lists themselves, so it does not go stale.
- **Custom endpoints remember their model list.** Ask once, with a Refresh
  button and the age of the list next to it, instead of asking your endpoint
  every time you open the picker.
- **You can see how much of a request was cached.** The token tooltip now shows
  the share the provider served from its cache, when the provider reports it.
- **The model list tells the truth about what it can do right now.** When a
  smaller local model is given a reduced set of tools, it is told so, instead of
  promising an ability it was not handed.

## v12.6.5 - Say What Happened


A release about the difference between a screen that says something and a
thing that happened. Two answers people were promised, a tool family that
reported success it had not earned, and the first accessibility check this
app has ever had that actually runs.

It also clears out a set of things that were there but could not be got at: an
endpoint you had configured and could not pick, a second one you could not
find, a number under an answer that read as waste, and a grid you scrolled past
every day.

### Added

- **The system appearance setting follows the system.** Choosing "System" used
  to read your operating system once and remember the answer, so it never
  noticed a later switch and the button never stayed selected. It follows now,
  live and after a restart, and the sun and moon in the sidebar set the same
  setting the appearance row does instead of a second copy of it.
- **A busy Skales IQ says it is busy, and shows you the way on.** When the
  service cannot take a request, the message says so in your language and
  offers the next step in the same breath: connect your own provider. During
  setup the Skales IQ card stays where it is, says why it could not start, and
  leaves the other three options one click below instead of finishing setup in
  silence with no provider at all.
- **An accessibility check that runs.** Six core screens are measured against
  the WCAG AA rules and walked with the keyboard, and the check is part of the
  toolbox rather than something someone remembers to do.
- **Every extra endpoint can be made the active provider.** The additional
  OpenAI-compatible endpoints could be chosen per chat but could never become
  the one Skales routes to by default. Each one has a Set Active button now,
  like every other provider card.
- **A way to add a second endpoint from where you already are.** It used to
  live only behind a dashed tile up in the provider grid, which is above where
  you are standing when you configure the first one. The Custom endpoint card
  offers it directly and takes you to it.
- **The provider grid folds away, and remembers.** A header with a chevron
  above the tiles: fold it once and the card you actually came for is at the
  top from then on, after a reload and after a restart. The header still says
  how many providers are switched on, and a search opens the grid so a match is
  never hidden behind the fold.
- **The token count under an answer says what it is made of.** Hovering it now
  breaks the input half into the two things it consists of: the tool
  definitions and system prompt that are the same on every turn and are what
  makes the agent able to act at all, and your own message plus the
  conversation. Nothing is deducted and nothing about billing changes - the
  number was always this, it just never said so.
- **The guide says which providers work where.** Some services do not serve
  every country, which is a fact about them and not about Skales. The
  Providers chapter now lists the full replacements, including the local one
  that always works.

### Fixed

- **The sidebar and dashboard provider status is green again the way it always was:**
  an active provider with a key shows Connected immediately. The stricter
  proven-connection state briefly shipped here had no way to ever turn green for
  the provider and read as offline on every install. Live proofs are still
  recorded quietly (a successful chat turn or provider test), and while the
  status is loading the sidebar shows a neutral placeholder instead of flashing
  red with a guessed provider name.

- **Windows finds npm and Node.js again during browser-automation setup**, even
  when Node was installed last: a corrupted PATH entry hit exactly freshly
  installed Node, so everything worked in PowerShell and failed in Skales.

- **A goal that says it is finished, and a check that says otherwise, now ask
  you.** They used to argue in private: the run declared the task complete, the
  check refused to confirm it, and the run was pushed straight back into the
  same step. One run said it was done twelve times over twenty steps and only
  stopped because someone stopped it. The disagreement comes to you instead,
  with both sides on one card and three answers: accept it as done, keep
  working, or stop. A run that keeps repeating the same closing summary with
  nothing new behind it now stops on its own and says so, and it says the same
  paragraph once instead of collecting a copy of it every round.
- **A goal shows you its checklist before it starts,** and an instruction you
  wrote down in plain words ("use at least three parallel strands") stays on
  that checklist word for word instead of being summarized into something
  general and rediscovered twenty steps later.
- **WhatsApp is either connected or it is not, everywhere.** Settings said
  "Connected as ..." with your number on it while Skales itself was told
  WhatsApp needed setting up, so it would decline a message it could perfectly
  well have sent. Both now read the same answer. Telegram got the opposite
  correction: a saved bot token with no chat paired to it counted as ready even
  though nothing could be delivered.
- **A WhatsApp message says what happened to it.** All three send tools
  reported plain success whether the message went out, was merely handed over,
  or went to a number that has no WhatsApp at all. They now say delivered,
  handed over but not confirmed, or not delivered and why, and a number with no
  WhatsApp account is refused instead of quietly accepted. The three also share
  one recipient rule: a contact that works in one of them works in all of them,
  and "unknown number" and "sending to this contact is switched off" are no
  longer the same sentence.
- **One tooltip per button.** Every icon in the chat header showed two bubbles
  stacked on each other, and the expanded sidebar put a tooltip over the row
  below the one you were pointing at. There is one tooltip, and only where the
  label is hidden.
- **The keyboard shows where it is.** The chat composer looked identical
  focused and unfocused, so there was no way to tell it had the keyboard.
- **Muted text is readable.** Hints, captions and the "Configured" badge were
  below the contrast a normal text needs in every light theme, and in Neon.
  Same colour family, dark enough to read.
- **The selected temperature unit can be read** in a light theme, the Flow
  surface can be scrolled from the keyboard, and the information button on the
  Agents page has a name and opens for the keyboard too.
- **A file tool that fails says why.** Listing, moving, copying, creating a
  folder and unpacking an archive reported that something went wrong without
  saying what, and all of them told Windows and Linux users to open a macOS
  settings page. Moving into a folder that does not exist yet works, the way
  copying already did, and an archive says whether a file was left out because
  of the size limit or because it was trying to write outside its folder.
- **Typing into a web page reports where the text went,** and says so plainly
  when nothing had the keyboard and the text went nowhere. Scrolling says where
  on the page it ended up instead of always saying "scrolled down". A click
  placed by the vision model says where it clicked and whether anything
  changed. Attaching a file names the field it attached to and refuses to guess
  when the page has more than one.
- **Reading aloud stops calling silence a success.** A voice that could not be
  played counted as read, so the next voice in line was never tried.
- **A team run writes its result into the conversation it happened in,** not
  into whichever one you had open when it finished.
- **The question card in Skales Code keeps every answer.** It sent on the first
  click, so a card with three questions answered one and threw two away.
- **The Google authorization page speaks your language,** and the camera move
  you picked keeps its name after you browse another category.
- **Product names stay product names.** Seventy-two labels across the eleven
  translations had turned Discover, Swarm, Autopilot and Wrapped into ordinary
  nouns, so the thing you read about could not be found in the app.
- **The Russian first screen is Russian.** The privacy and autonomy notice
  everyone accepts before Skales starts was Russian written in Latin letters,
  as were several settings tabs.
- **The roadmap page shows the last three releases,** the guide explains why a
  long unattended run can end when a disk parks itself, and the camera-move
  count in both is the number that is actually there.
- **A model you typed in yourself is findable in the chat model picker.** The
  Custom endpoint has no catalogue to offer - the model is whatever you point
  it at - and that turned out to mean its model appeared in no list the picker
  reads. So an endpoint that was set up, switched on and working could not be
  chosen for a chat unless it happened to be the active provider already. Any
  model you configure on any provider card is searchable now.
- **The extra endpoints speak your language.** That whole block was English
  only, in all twelve languages.
- **A local Qwen 3.6 acts instead of printing what it meant to do.** The model
  writes its tool calls in a shape Skales did not read, so a request to look at
  a folder came back as a line of raw text where the answer should have been.
  Skales reads that shape now, and Qwen 3.6 has settings of its own instead of
  borrowing an older model's.
- **The Open Flow button in Studio can be read.** White on the accent colour was
  below the contrast text needs, on that button and on six more like it. They
  take the readable colour for whichever accent you are running.

## v12.6.2 - Nothing Green Without Proof

Two people wrote in the same night to say Skales had told them a file was saved
and the file was nowhere. A third could not connect her iCloud calendar because
that connection had never been able to work. This release is about the gap
between what the screen says and what actually happened.

### Added

- **A connection is green only after it was tested.** Every "Connected" and
  "Active" badge in Settings now means a live test reached the service and
  passed, and hovering it says when. Credentials you saved but never tested read
  "Configured" instead: the card still looks set up, it just stops promising a
  connection nobody has made. Saving or changing a credential drops the old
  result, so a passed test can never vouch for a new key.
- **Test buttons where there were none.** Notion, Todoist, Spotify, Home
  Assistant, Replicate, Stability, ElevenLabs and Runway can be tested from
  their own card, and Slack, Signal, X and your Google account got a button for
  the check that already existed behind them and was never run. Kling, Fal and
  Atlas Cloud have no such check, so they stay honest at "Configured".
- **Apple Calendar works.** iCloud refuses every request against its front door,
  so Skales now walks the discovery path Apple expects, finds your calendars and
  writes to a real one. You can pick which calendar new events go into, and a
  refused connection says whether it was the password or the address.

### Fixed

- **A saved file says where it is.** Generated images, videos, voice files and
  merged clips now report the full path they were written to instead of a bare
  filename or a folder nobody could locate, the answer card shows that path with
  a button that opens it in your file manager, and Skales is no longer allowed
  to tell you a file was written unless the tool that wrote it says so.
- **Answers no longer carry internal markers.** A model that quoted a tool
  result word for word used to quote the fences around it too, and they landed
  in the answer. The markers come off, the quoted text stays.
- **The token line under an answer reads correctly.** It showed one number that
  looked like the cost of the reply while it was mostly the question, the
  instructions and the tool list. It now shows the two directions separately.
- **The VirusTotal key check asked an address that does not exist,** so a valid
  key was reported as invalid. It also tested the dots the field shows instead
  of the key you saved, so the answer was "invalid" every time after a save.
- **LinkedIn posts and uploads use the current API,** and fall back to the older
  one only when LinkedIn says this app may not use the new one.
- **Microsoft mail says what it can and cannot do.** Work and school accounts
  cannot be connected with a password at all; personal accounts usually can with
  an app password. A refused send now explains itself the same way a refused
  inbox already did.
- **A file written while the disk is waking up is written, not lost.** Scheduled
  jobs running unattended could fail on a write that a moment later would have
  worked.
- **Skales Code on the phone shows your coding sessions again,** even when you
  have thirty newer chats, and archived sessions stop appearing as live work.
- **Switching a team off during its final step no longer writes a verdict over
  the run you cancelled.**
- **Signing out of the Google account clears the form it was set up in.**

### Changed

- The model picker mentions the four thinking levels once, so the strongest one
  is not something you have to find by accident.
- Release builds fetch the Linux package before writing the update manifest, and
  refuse to write a manifest that silently leaves it out.

## v12.6.1 - Order and Follow-Through

The release after the big one is where your first week of feedback lives. The
provider page you can finally read, agents that take turns on one graphics
card, a scheduler that owes you nothing after a night of sleep, and a dozen
small wrongs made right.

### Added

- **The provider page is a grid you can read.** Every provider is a tile with a
  short description, badges for what it can do (voice, vision, media) and a
  switch. Switched off means gone, from the page and from every model picker.
  Filters find providers by capability, and "+ Custom endpoint" is a tile like
  the others. Choosing your active provider works exactly as before, and no
  setting was removed: the advanced ones now live in the provider's own card or
  in one folded Advanced Routing section at the end.
- **Tencent Hunyuan is a provider**, with a switch between its regions.
- **Agent teams can take turns.** On local models, running several agents at
  once means they fight for the same graphics card. Sequential mode runs them
  one after another and unloads each model where the runtime supports it, and a
  new "Keep models loaded" setting for Ollama puts that duration under your
  control, from unload-immediately to always.
- **MCP servers that use a plain API token** get a headers field right in the
  form, and a rejected request no longer pretends the server wants a sign-in.
- **skales:// links.** A link can open Skales and land on the right screen.
  Nothing is downloaded or executed without a confirmation card first.
- **Bring your own vector database.** Qdrant and ChromaDB work as retrieval
  backends beside the built-in store, and the built-in one remains the
  fallback.
- **One Telegram bot, several chats.** Pair additional chats and choose where
  an answer goes.
- **The scheduler survives sleep.** Runs missed while the machine slept are
  caught up on wake, and a timer that the system froze says so instead of
  silently drifting.
- **A headless start** for machines without a screen, reachable through the
  existing web access, and **a tiny-model preset** that trims tools and context
  so sub-1B models stay usable.
- **The guide explains the three multi-agent modes** and when to reach for
  which.

### Fixed

- Buttons and links that were invisible on dark themes. A whole family of dead
  color references, all of them found and replaced, with a check that keeps the
  class out.
- Signing in with your Google account now unlocks Drive and Docs everywhere,
  not only Calendar, and each card says which login it is actually using.
- The voice list loads for every provider, not only one, and the preview button
  plays a sample on each.
- Conversations keep a timestamp per message instead of a clock in the prompt,
  which keeps long conversations faster and cheaper.
- The navigation menu no longer hides behind page content.
- Local models that write their tool calls as plain text in one more dialect
  are understood and executed instead of leaking markup into the answer.
- An agent that stops after a failed tool now says which tool failed instead of
  returning nothing.
- In team runs, your agents' own instructions outrank the coordinator's
  boilerplate.
- A crash remembers where it happened: the next bug report
  you send automatically carries the screen and the component that crashed,
  so you do not have to.

## v12.6.0 - Iris Orbit

The release about things that were happening where you could not see them, and
about who decides once you can. A decision waiting below the fold, a message
that never arrived, a browser session frozen behind a dialog nobody mentioned.
Then: a team of agents that ranks its own answers and hands the choice back to
you, a plan you read before it is written, and a camera move you pick before the
shot is filmed. Skales recommends. You decide.

### Added

- **Iris Orbit is switched on when you install Skales.** The release is named
  after her, and a surface you have to find in Settings before it exists is a
  surface nobody finds. Nothing else changed: if you turned her off, she stays
  off. Without a provider key the window says so and offers the way to Settings,
  Voice, the way every other surface that needs a key does.
- **"What can I say?" is a list you can read.** Every sentence Iris understands
  was already there and written down nowhere: turning her into a shape, asking
  for something as a document, a web search, a timer, your tasks. The window's
  menu shows all of it with a real example each, in twelve languages, and asking
  her out loud what she can do gets the same answer without a tool call.

- **Iris Orbit: a way to talk to Skales instead of typing to it.** A window of
  its own, black, with no toolbar and no message list: a particle eye in the
  middle and one quiet line of text under it. Hold the space bar and speak. Iris
  hears when you stop talking and answers on its own, out loud, and the line
  under the eye shows what is being said, sentence by sentence, like a subtitle
  rather than a chat log. The eye reacts while it happens: it warms and pulses
  with your voice while it listens, looks away while it works, and becomes a
  circular soundwave while it speaks. It follows your cursor when it is idle,
  and clicking sends a ripple through it.
  Iris is a face on Skales, not a second assistant. The same conversation, the
  same model, the same tools, the same memory: ask it to forget something and it
  is forgotten everywhere. Iris opens where you left off and says so, "new
  conversation" starts a fresh one, and any chat you are already in can be
  carried over by voice from the button in its header, with everything that was
  said so far coming with it. Go back to the chat afterwards and the spoken
  turns are simply the newest messages in it.
  If speaking is not on right now, start typing: a single line appears over the
  eye, Enter sends it, Escape puts it away again. "New conversation", "my
  tasks", "timer ten minutes" and "forget X" are answered directly, with no
  model involved at all. Cmd or Ctrl plus K brings up the earlier conversations;
  Escape stops whatever is running, and closes the window when nothing is.
  The first time you open it, Iris introduces itself. That plays once.
  The window takes your own light or dark theme, like the Code and Flow windows
  do: a glow on black at night, dark points on a light ground by day.

- **The eye answers with more than words.** Every command Iris can answer
  directly now has its own visual reply. Set a timer and the particles become
  the remaining time, spelled out as digits that count down. Ask how far along a
  goal is and they draw the progress as a bow. Ask her to forget something and
  they scatter and gather again. Start a new conversation and the eye blinks. A
  reminder going off sends a ripple out from the middle.
  You can also just ask for a shape: "morph into a car", "become a feather", in
  any of the twelve languages. There are about fifteen hundred of them, and they
  turn up on their own as well: write down "call the dentist" and the eye
  becomes a telephone, and while Iris is talking the particles quietly take the
  shape of whatever is being discussed. That last one can be switched off under
  Settings, Voice, if you would rather the eye stayed an eye.

- **Results appear inside the eye instead of opening windows.** A picture, a
  document, search results, a short list: the ring leaves the eye, becomes the
  border of a panel, and the result appears inside it. One at a time, and Escape
  puts it away and brings the eye back. Heavier things are handed to the window
  that already does them - a page goes to the browser, a render goes to Studio,
  code goes to the Code window - and Iris tells you where she put it.
  Cmd or Ctrl plus slash opens Help, which lists every command, what it does,
  which answer the particles give, and a sentence you could actually say.

- **Iris does the work, not just the talking.** "Twenty-five minutes of focus"
  starts a timer you can pause and resume by voice or by clicking; when it runs
  out, Iris says so. "What is on today" reads your tasks aloud and shows the
  list, and you can tick them off from there. "Show me the status" reads out how
  far along a goal is. All of it goes into the lists, the schedule and the goals
  you already have - there is no second copy anywhere. Anything that finishes
  while the window is open is spoken as it lands; anything that finishes while it
  is shut is waiting to be read out the next time you open it.

- **Settings, Voice: one place for all of it.** The voice that speaks, the ear
  that listens and Call Mode used to be three cards under Integrations, next to
  Notion and Spotify. They have moved to a tab of their own, together with the
  things that never had a home: how long a silence ends your turn, which key is
  push-to-talk, and what happens when you talk over an answer. There are named
  presets for a local Kokoro and a local Whisper that fill in the endpoints for
  you, so running your own voice on your own machine is something you can find.

- **A wake word you teach yourself.** Say "Iris" three times in Settings, Voice,
  and she learns your pronunciation - yours, not a model's idea of it, so it
  works whatever your accent. Then you can just say her name instead of holding
  the space bar. Everything happens on your machine: the recordings become
  numbers there and are dropped, and nothing is uploaded. When the ear is open
  the window says so; there is never a microphone listening in silence.

- **Talking over her stops her.** With any provider at all, the microphone stays
  open while Iris speaks, and starting to talk cuts her off - what you said
  becomes the next thing she answers, the way it works with a person. Where a
  provider offers a live audio channel of its own, that can be picked instead;
  where it does not, the option says so plainly rather than quietly doing
  something else.

- **A word while a slow model is still thinking.** Some models take several
  seconds before the first word arrives, and a screen with nothing on it but a
  pulsing dot reads as broken rather than as busy. If nothing has arrived after
  a second and a half, the small assist model you set under Settings, Goals,
  Light-pass model writes one short line about what you just asked, in your
  language, into the same place the thinking indicator sits. It disappears the
  moment the real answer starts, it is never part of the conversation, and it is
  never sent to a model again. With no assist model set, and on a model that
  answers quickly, nothing extra happens at all.

- **A team of agents has someone in charge of it, and you have the last word.**
  The first seat of your roster is the coordinator now. Before anyone answers it
  reads the assignment and breaks it down: what a complete answer has to cover,
  and which angle each agent leads on. Every agent then answers the whole thing
  with that brief attached, on its own model. Afterwards the coordinator compares
  what came back and writes a verdict: a ranking, two sentences per agent saying
  why it sits where it sits. No scores, no marks out of ten. The verdict
  recommends and decides nothing, and it says so.
  Every agent card carries two buttons. "Continue with this agent" switches the
  session to that one agent and hands its answer to the next turn as the thing to
  build on. "Take this result" puts the answer into the conversation, or saves it
  as a file where there is no conversation to put it in. Both survive a reload.
  The same cards and the same two buttons now appear everywhere several agents
  produce results: chat, Skales Code, Tasks, Group Chat and the Organization
  result list draw one component instead of five versions of a list.
  Every way a run can end is its own state rather than an error message in
  English. One agent failing leaves a ranking of the rest; all of them failing
  leaves an honest line instead of an empty verdict card; counting the Agents
  button back to one ends the run instead of leaving it working behind a door you
  just closed; and a run whose process died is recognised when you reopen Skales
  and closed off, keeping whatever had already arrived.

- **Flow can film a shot, and you pick the camera move first.** A new chip in
  Flow: Film. It makes one clip with one deliberate camera move, chosen from a
  grid of fifty-one moves grouped by what they are: zoom, dolly, crane, pan,
  orbit, rig, aerial, lens. One move per clip and no way to pick two, because
  mixing them is what makes generated footage look generated.
  The moves are Skales' own film language, not a pointer at somebody else's
  preset library: each one carries its own formula in three parts, where the
  camera sits, what it does and how the move runs over time, and that formula
  shapes the prompt for whichever backend makes the clip. So the order starts
  with your own keys: the video APIs under Settings, Skales Studio first, then a
  Hugging Face Space you have activated, then a connected media server such as
  Higgsfield if you happen to have one, as the optional connector it is.
  With none of the three set up the chip does not disappear and does not pretend:
  it says which key is missing and where it goes, and it keeps the move you
  picked. Follow-up takes build on the last one instead of starting over.

- **Seedance and Atlas Cloud in Settings, Skales Studio.** Seedance was in the
  list and could never have worked: both of its model rows named an address that
  does not exist, so every attempt came back as not found. It runs on fal now,
  which is ByteDance's own route and the only one carrying Seedance 2.0, and the
  rows for people whose key is at Replicate are repaired rather than dropped.
  Atlas Cloud is new: one key across several hundred models for chat, image and
  video, billed per use, and it shows up as a provider in the same three places
  every other one does.

- **Skales asks you one question, once.** If you were already using Skales
  before this version, a box appears once and asks what is missing, what could be
  better, what you actually use it for, what you love and what you do not. One
  text field, any language, as long or as short as you like. It goes straight to
  Mario and it is not published anywhere. A checkbox under the field links to
  the privacy policy, and nothing is sent before it is ticked. "No thanks"
  closes it for good, and a fresh installation is never asked.

- **Hugging Face Spaces count as a media backend in Flow.** The integration has
  existed for a long time and Flow was built straight past it: a Space you had
  activated sat in the model's tool list and was never offered here. Image, video
  and film all know about them now, as the free-or-nearly-free route with a
  shared queue.

- **More than one agent can answer, and you can see each of them.** The composer
  gets a button next to Auto with a number in front of it: how many agents your
  next message goes to. One is off and means what it always meant - the default
  agent, or whichever one you picked for this chat. Every click counts up to six
  and back to one. A run of more than one shows up as cards, the same cards tool
  calls and diffs already use: one for the run, one per agent that opens to that
  agent's answer, and a summary at the end. Who takes part is arranged on the
  Agents page, in a section above the cards; the order in that list is the order
  they run in, so "3 Agents" means the first three. The first seat belongs to the
  default agent and does not move, because somebody has to write the summary.
  Tasks can be run the same way from the create dialog.

- **One Google account instead of the same account asked for five times.**
  Calendar, Drive, Docs and YouTube each wanted their own credential for what is
  one Google account. Sign in once under Settings, Integrations and it covers all
  four. Every one of those fields still wins whenever it holds something, so
  nothing you already set up changes; with no account connected nothing changes
  at all.

- **An archived coding session can be looked at again.** Skales Code could put a
  session away and nothing anywhere could bring it back. The session list has an
  archive icon at the right of its heading now: one click shows what was
  archived, with Restore in the right-click menu, another click returns. The
  search box works in both.

- **Skales can give a web page a file.** Attaching a picture to a post used to
  end in the operating system's file dialog, which Skales cannot use and which
  freezes the browser until you close it by hand: an agent would try eleven
  other ways and post without the picture. The file now goes to the page
  directly, no dialog involved, and a click that would have opened one is
  refused with the way that works instead.

- **A browser session that is stuck says so and stops.** It used to keep trying
  until the budget ran out, because from the inside a blocked page and a slow
  page look the same. Now it recognises being held, ends, and tells you why.

- **You can see which sites Skales is signed in to, and sign out.** Skales
  browses in one profile, so a login stayed until you deleted a hidden folder -
  and there was no list of what was in there. Settings shows the sites, signs
  out of one, or clears the profile completely. It refuses while a browsing
  session is running, because the browser writes the profile back when it
  closes and the deletion would quietly be undone.

- **Replace every occurrence in a file in one go.** Changing the same thing in
  seventy places meant seventy edits, and it was easy to lose the thread halfway
  through. One call now does the file, by exact text or by pattern, and says how
  many places changed.

- **A permission you can answer once for the whole session.** Flow could ask the
  same question ten times in a row with only two buttons on the card. There is
  now a third: allow this kind of action for this session. It covers the action
  AND the reason it was stopped, so agreeing to a command inside your project
  folder is not agreeing to one that reaches outside it. It lasts for the
  session, is never saved, and lifts no safety guard. Under Auto, confirming
  once is enough - which is what choosing Auto was supposed to mean.

- **Your phone can watch a team work.** Starting a team task from the phone used
  to show nothing at all until it finished. The Organization view now sees who
  is working, who is waiting and what tool is running, the same live picture the
  desktop has.

- **Your own transcription server has its own settings.** It shared a card with
  text-to-speech and only appeared when you set the SPEAKING provider to Custom,
  so running your own transcription meant changing an unrelated setting first.
  It sits in the Speech-to-Text section now. Both directions also take extra
  parameters for your server - temperature, language, guidance, steps, whatever
  yours has - as one field rather than a fixed list that would go stale.

### Changed

- **The local voice engines are marked as what they are: optional, and not
  Docker.** The card offering Kokoro and Whisper showed a `docker run` line and
  nothing else, which read as though Skales wanted Docker. It says plainly that
  Skales itself never needs it, carries a Docker-free command for each engine
  beside the Docker one, and is labelled Optional. Ask Skales to install either
  one and it now walks the whole route for your system, health check and
  uninstall included, through the normal command approval.

- **Iris Orbit says it is a first version.** A quiet "Beta v1.0" in the corner of
  the window and a line in its settings block, plus the licence the engine is
  under and where to read it.

- **The sidebar's Iris entry is an eye.** The dot ring stays where it means
  something: the window, the settings block and the logo.

- **The user guide covers the shared Google account and Iris Orbit.** Neither
  was in it. The Google chapter walks the Cloud Console setup end to end, says
  which client type to pick, and explains why a service's own key still wins and
  why adding a service later asks for consent again. The Iris chapter covers
  opening it, speaking, typing, morphs, timers, the wake word, the right-click
  menu, languages, and where the conversations live.


- **A plan is something you read before it exists.** "Generate Master Plan" used
  to create every task in it in the same breath, so the first time you saw a plan
  it had already been written into your board. It is a draft now: a list you
  read, untick and edit, and only "Create these" writes anything. A task the
  model got wrong no longer takes the whole plan down with it either. The usable
  ones survive, and the ones that were left out are named with the reason.

- **A Quick Task is a task, not a title.** One line still starts it, but what
  gets created carries instructions the agent can act on, the project it belongs
  to and the agent best suited to it, each one a suggestion you untick if it is
  wrong. And the warning that the task will not run while Autopilot is paused is
  the switch itself now, where the warning is, instead of a sentence about a
  button elsewhere.

- **Cards on the Autopilot board move by dragging.** Two moves are refused rather
  than faked: you cannot drop a card into "in progress", because that is where
  Skales puts a task when it actually picks it up, and you cannot drag one that
  is running out from under the runner. A move also clears what the new column
  would otherwise make untrue.

- **The running-goals strip stops being a shelf.** Open the goal where it lives,
  carry it on, or stop it, without leaving the page.

- **The composer bar never breaks into a second line.** In a narrow window it
  scrolls sideways instead, the way Code and Flow already do, with the context
  readout on the left pinned where it can always be read.

- **Skales is counted while it is running, not while Discover is open.** The
  online figure came from the Discover tab and only from people with a gamertag,
  which meant it was measuring almost nobody. It now reflects running apps.

- **What belongs to the conversation stands in the conversation.** The live view
  of the browser, the plan the model proposes and the offer to turn a chat into a
  goal used to hang above the text box in their own sizes, outside the
  conversation. They sit under the last message now, the width of a message, and
  scroll away with everything else that happened.

- **The user guide is user help.** Five chapters of release notes had grown into
  it, the newest at the very top, so the first thing a new reader met was a list
  of things they did not know they were missing. They are gone; what changed in
  a release is in the changelog, which is where you are.

- **Skales IQ: internal routing update.** Nothing to set, nothing to notice.

### Fixed

- **What Iris writes appears in the ring, not as a clipped line under the eye.**
  Ask her for a poem, a list or a summary and the text lands inside the frame,
  readable, with one short spoken sentence beside it. Until now the ring only
  ever opened for a tool result, so an answer that WAS the thing you asked to see
  was cut off mid-word in the subtitle line.
- **The Iris window no longer stands still for several seconds when it opens.**
  Opening it made four requests to the local server one after another, with
  nothing on screen moving while they queued. Three of them never depended on
  each other, and the app now warms the window's own route at launch as well.
- **Approving a file no longer throws you into the Code window.** Saying yes to
  "may I write this" is not asking to change windows. Iris says where the file
  went and stays where she is.
- **Iris asks for permission in her own voice.** The question used to arrive as
  a chat approval card over the eye. She now says what she wants to do, the two
  answers are hers, and "yes" or "no" out loud answers it in twelve languages.
- **Iris does not play the thing she turns into.** Asked to become a car she
  wrote a short story about being one, signed off as it, and quietly dropped the
  document that had been asked for in the same sentence. A morph is what the
  particles do; she stays herself, and the task in the sentence still gets done.
- **Iris says who she is in one sentence.** "I am Iris, the voice of Skales",
  written out per language rather than improvised, after a weaker model answered
  "I am Iris, Skales at the wheel".
- **She opens with something different each time.** Fifteen greetings per
  language, rotating, instead of the same sentence at every open.
- **Iris speaks like a person about her microphone.** "Ear open", "ear shut,
  voice off" was a literal translation of an English image into all twelve
  languages. It says listening, mute and unmute now, and the German, French,
  Croatian, Turkish, Portuguese and Vietnamese lines got their accents back.
- **Iris answers no longer ring a bell in the main window.** Every spoken answer
  produced a "message finished" toast and a chime in the window you were not
  looking at, and the Code window did the same. A companion window that is open
  is you being on that surface.
- **"Continue with this agent" folds the cards and gives you the composer.** It
  answered the question those cards were asking and left all six answers
  standing between you and the reply you now wanted to type. A conversation
  opened again loads its finished run folded, and the cards have a ceiling and
  their own scroll instead of growing over the input bar.
- **The working basis is a chip, not a wall of text.** Carrying on from an
  agent's answer put that whole answer into the conversation as raw text, and in
  the Code window it was glued onto the front of the next thing you typed, so
  your own "ok stop" came back with an essay in front of it. It is one line you
  can open, and it travels beside your message rather than inside it.

- **Iris keeps listening.** With the ear open, the microphone still closed after
  every answer and Space had to be pressed again to say the next thing. The ear
  now reopens by itself at the end of everything she says, until "Ear shut,
  voice off" or the window closes. That entry brings her back too.
- **A document Iris writes appears in the ring.** After the fix that stopped her
  framing the bookkeeping around a document, she framed nothing at all: the tool
  message is a receipt, and the document itself goes into this conversation's
  document panel. She now reads the receipt as what it is, a title, and puts the
  real text in the frame under its own name. The guide has a new section on what
  opens a ring at all: a picture, a document, a web search, a list.
- **A failed transcription says which provider refused, and why.** "No
  speech-to-text provider available" with a working key configured: every leg
  had been tried, each had thrown, and the reasons were only in the server
  console. The message now carries them ("OpenRouter: 402 ...; Groq: no key"),
  and a genuinely unconfigured setup still says that instead.
- **Iris no longer introduces herself as Skales.** On a long conversation a
  weaker model would read the voice profile, then four thousand tokens of other
  things, and open with an apology for the wrong name. The one identity line is
  repeated once at the very end of the prompt. Same profile, no second persona.
- **The relay stops fighting itself in the log.** `socket REPLACED` once a
  second, filling the console and burying every other line. The reconnect used
  to reset its backoff every time the relay let it in, so two connections
  claiming one identity evicted each other forever at exactly one second apart.
  Being replaced now backs off and the line is printed once a minute with a
  count, and it names the process it came from, which is the fact that answers
  it. The engine that opens those connections is also single now: nothing
  stopped a second one being started over a running one.
- **A stream that ends twice no longer throws.** `Controller is already closed`
  in the console, from closing a stream whose reader had already gone, in fifteen
  places across the update, build, plan and pull routes.
- **Six debug lines per model response are gone from the console.** They printed
  the raw provider answer, including its content, on every step of every turn.
  One line is left, and only when a step really came back empty, saying what
  that means. A model that spends its whole reply budget on reasoning already
  ends in a proper answer that says so.
- **Iris' voice is a list you can read.** It was a text field asking for a
  provider voice id in the provider's own spelling, which nobody knows by heart.
  It is now a dropdown of the voices your TTS provider actually has, fetched
  from the provider rather than baked in, so a voice you cloned five minutes ago
  is in it. "Provider default" leads. Where a provider has no list to fetch, it
  says so and the field stays, so an id you do know still works.

- **A run with several agents survives a reload.** Asking a team of agents a
  question in a fresh conversation started the run against a conversation that
  did not exist yet: the question, every answer and the verdict were written
  nowhere, and reloading left only whatever was typed afterwards. The question
  now goes into the conversation before the run starts, the verdict is written
  into it by the server the moment it is ready, and reopening a conversation
  finds its run again and repairs a verdict that never made it in. Nothing in
  that path fails quietly any more: when something cannot be saved, the
  conversation says so at the time instead of losing it silently.

- **A finished team run can be folded away.** After the verdict has been read
  and an agent picked, the cards collapse to one line saying how many agents
  answered and whether there is a verdict, and open again on a click. The state
  belongs to the run, so a phone and the desktop agree about it. The whole run,
  every answer and the verdict, is also kept as one Markdown file next to your
  other documents, so folding the cards away never loses it.

- **The team cards no longer follow you into the next conversation.** Switching
  conversations, or starting a new one, left the previous run's cards sitting
  above the composer.

- **"Take this result" in the Code window puts the result in the session.** It
  used to trigger a browser download, which on a phone reaching the window
  remotely meant the answer ended in a downloads folder instead of the work.
  Pressing it twice also used to add the same answer twice; it does not now.

- **A team run started from the Code window's start screen is visible.** Sending
  with two or more agents before a folder was open started a real run that was
  drawn nowhere at all. The folder is asked for first, as it is for an ordinary
  message, and the run happens in the session that opens. The run is found again
  after a reload, and the agent picked to carry on is still picked.

- **Speech that could not be transcribed by your own server says so.** Pointing
  Skales at your own speech endpoint and having it go down moved your voice to
  whichever cloud provider had a key, without a word. It still keeps working,
  and it now tells you it happened. Transcription through Hugging Face also
  returned something that was not text at all, so Iris said "[object Object]"
  out loud and dictation in the chat failed with an empty error message.

- **The feedback form looks like a form again.** Its backdrop had a three pixel
  blur and no Safari equivalent, so through a browser it was a flat dark sheet
  over an app you could no longer see. It uses the same glass as every other
  Skales overlay now, in every browser. The same missing Safari line was fixed
  in twenty-odd other overlays across the app.

- **Settings stops telling you to update an app that is already current.**
  "Desktop Control" probed a bridge that only exists in the desktop window, so
  viewing Skales in a browser always read "Update to the latest build to enable
  replay". It now says which of the two situations you are actually in.

- **A voice setting that could not be saved says so.** The switch moved on
  screen and quietly went back on the next visit.

- **Iris works when you reach her through a browser.** Three things in her
  window asked the desktop app for something that only exists in the desktop
  app, and did nothing at all otherwise: "Voice settings" in her right-click
  menu, Escape on the resting eye, and handing a result to Studio, the browser
  or Skales Code - which she announced out loud while nothing happened. Each of
  them now opens the place it names, in whichever shell she is running in.

- **Iris can hear you.** On macOS the microphone was dead: the signed app was
  never given the right to use one, so the system never asked and Skales did not
  even appear under Privacy and Security, Microphone. It asks now, and if the
  answer is no it says so and opens the right settings pane instead of listening
  at nothing. When speech cannot be turned into text, the reason is said out
  loud and written under the eye rather than swallowed: "I could not understand
  you" with what actually went wrong, in place of silence.

- **After the greeting, Iris listens by itself.** No key needed: the ear opens
  when the last word of the welcome has been spoken, the line under the eye says
  so, and it closes again when you stop talking, like any other turn. The space
  bar is still there for when you want it, and holding it down no longer cancels
  the very turn it started.

- **Right-clicking in the Iris window does something.** New conversation, your
  earlier conversations, the voice settings, copy the last answer, ear shut and
  voice off, close the window. There is also a small cross at the top that
  appears while the mouse is moving and fades away when it rests, so the window
  can be closed without knowing that Cmd or Ctrl plus W works. The buttonless
  surface stays buttonless.

- **Iris speaks your language.** The welcome was always English, whatever the
  app was set to, because the greeting was written before the language had
  finished loading. It now follows the app's language like everything else, and
  Iris answers in it too. There is no separate language setting for the voice:
  one language, set in one place.

- **The name question takes Enter.** Answering the "what should I call you"
  prompt with the Return key did nothing at all, and only Skip worked. Typing
  and pressing Enter now answers it, and an empty Enter means "no name, carry
  on".

- **Typing to Iris is findable.** The text field appears when you type and is
  invisible until then, so it was easy to miss entirely. A single quiet line
  says so once, and never again after you have used it.

- **Wake-word training says what happened.** It could fail six different ways
  and reported five of them as "no microphone", including the case where the
  speech detector simply had not loaded. Each one now names itself. There is a
  Stop button that stops it, a Start over button, a live level bar so you can
  see the microphone react while you speak, and a microphone picker for machines
  with more than one input - which the whole voice loop then uses, not just the
  training.

- **A team of agents answers a typed question too.** Setting the Agents button
  above one and then typing ran the message as a single ordinary turn: only
  messages with a picture attached ever reached the team. It also says up front
  when your roster is smaller than the number you asked for, instead of quietly
  running fewer, and an answer that came out of a team says how many agents made
  it rather than naming one model. Chats that ran a team are marked in History.

- **Spoken conversations are marked.** They were always ordinary conversations
  in the ordinary list - which is the point - but nothing said which ones had
  been spoken. History marks them, and reopening one in the chat shows which
  turns were said out loud.

- **The border around a result has no gap, and fits the answer.** The frame the
  particles form was missing its top right edge, and it was built at a fixed
  size before the panel existed, so a two-line answer got a full-page border. It
  is now measured from what is actually there, and the line of text moves with
  it. In a narrow window the eye no longer runs off both sides.

- **The one question about how Skales is going comes back.** Telling Iris your
  name rewrote the identity file, and the "is this install old enough to ask?"
  check read that file's date - so answering Iris reset the clock and the
  question went quiet for another three days. The clock is now set once and
  nothing can move it.

- **Running out of Skales IQ credit is a state, not an error.** A turn that hit
  an empty balance ended in a red error with a provider message in it, and a
  goal or a scheduled task that hit it was marked failed, its work thrown away.
  It now says what happened in your language and offers the way on: put your own
  key in, and carry on. A goal is paused rather than failed, keeps every step it
  had already done, and continues from there instead of starting over. Nothing
  is retried against an empty balance any more, so a background task no longer
  spends attempt after attempt on a request that cannot succeed. A user on their
  own key never meets any of this.

- **Stopping a team of agents stops the one that is already answering.** Counting
  the Agents button back to one ended the run, but an agent already talking to
  its provider carried on generating an answer nobody would read, on a metered
  connection, until it was finished. It now stops mid-sentence. Its card says it
  was stopped rather than claiming it failed, and an answer that had already
  arrived stays where it is.

- **A long message from us is readable on the phone.** Broadcast texts stopped at
  about sixty characters on a phone while the computer showed all of them, and a
  message with no link behind it had nothing to open. The full text is shown, and
  a long one opens out with a tap.

- **A team of agents on the phone no longer gives up over a stray bracket.** If
  the agent writing the plan added a friendly closing line, the plan was read as
  unreadable and the whole run ended, over a plan that was perfectly good.

- **Dragging a link no longer shows you a web address.** Nudging any menu entry
  painted the browser's own drag preview, with the page title and a localhost
  address under it. A desktop app should not show its web insides, and nobody who
  brushes a menu item while scrolling asked to see the address of anything.

- **An agent you edited by hand is not overwritten by a page that was open.**
  Agent definitions are files, and a prompt shortened in the file could come back
  longer the next time the Agents page saved. Every write stamps the file now,
  the bookkeeping the app does after a run merges into the file as it is at that
  moment rather than a copy read minutes earlier, and a save that would clobber a
  newer file is refused and says so instead.

- **The Studio upload to YouTube works.** It read a setting nothing in the app
  ever wrote, so it could not publish at all, whatever you had connected. It now
  asks the same place the rest of the app does.

- **The YouTube tools are offered to anyone who has YouTube.** They only appeared
  for people with a separate API key, and stayed hidden from anyone whose Google
  account already covered it.

- **A saved key shown back as dots can no longer be saved as dots.** Reloading
  Settings filled the Drive, Docs and YouTube fields with a masked stub, and
  pressing Save wrote the stub over the real credential.

- **A decision waiting out of sight is visible.** In Skales Code the transcript
  could grow past a permission card sitting below the fold, with the session
  paused on it and nothing to say so. There is a jump-to-latest arrow now, and
  when something down there needs an answer it becomes a marked badge with a
  count.

- **Skales Code names its sessions.** Every one of them was called "New Chat",
  forever, which made the history a wall you could not find anything in. A
  session is named after the work after its first exchange.

- **Commands find the tools your terminal finds.** `npm` could be "command not
  found" in a Skales Code session while it worked perfectly in your own
  terminal, because an app started from the Dock does not inherit your PATH.
  It does now.

- **A message you queued gets delivered.** Typed while a long task was running,
  it could be dropped when the task finished instead of being sent as the next
  turn.

- **Flow shows a finished page in its own preview.** It would ask for approval
  and then open the page in your system browser, while the preview panel beside
  the chat was already showing that exact file.

- **An agent reads the assignment you actually gave it.** A background task's
  instructions were welded together with the harness's own rules into one block,
  so the agent was free to summarise the lot and work from its summary - which
  is how an assignment came back broader than it was written, with examples
  nobody had supplied. Your words now arrive exactly as written and marked as
  yours.

- **Publishing a subfolder no longer overwrites the homepage.** Publishing sends
  the CONTENTS of a folder, so forgetting to name a destination put a subpage on
  the site root. It happened twice. It now asks where the folder should go.

- **The tool list knows about every tool.** Five that exist were missing from it,
  so anything asking "does this tool exist" got the wrong answer.

- **Discover download figures respect the platform filter.** Filtering to one
  platform still summed the downloads of all of them.

## v12.5.76 - Two Corrections

A light window stays light when you have picked your own colours, and connecting
a Google Calendar works on an account made after 2022.

### Fixed

- **Light mode with your own accent colours.** Switching to light while a custom
  accent was set left the dark background in place and turned the text dark with
  it: a page you could not read. The colours are worked out again the moment you
  flip the switch, which is the step that was missing. If you are on 12.5.75 and
  see this, "Back to the original" in Settings clears it at once. Nobody who kept
  the shipped colours was affected.

- **Connecting Google Calendar.** Google stopped accepting the copy-the-code
  redirect for any OAuth client created after 2022, so a new client could not
  finish the connection at all. Skales now sends Google back to a small page of
  its own on your machine, which shows the code with a copy button. Step two in
  Settings is unchanged: paste it and you are connected. Nothing to register on
  Google's side, because a desktop client may use any local address. An older
  client keeps working exactly as it did.

## v12.5.75 - Extensions

Skales says out loud that you can build onto it, and the Code window stops
tripping over its own defaults.

### Added

- **The Extensions pill.** The Code composer has a new toggle next to the
  attachment clip. Switch it on and the session knows, precisely, how Skales is
  extended: how a capability that worked once in chat becomes a permanent tool,
  how an app you build is registered as an API Connector Skales can call, how
  your app calls into Skales over the DevKit remote API, and how an MCP server
  plugs its tools straight in. The contracts are spelled out in the prompt, so
  a small local model can follow them as well as a frontier one. The tooltip on
  the pill says all of this before you press it.

- **Skales knows it is extensible.** Asked "how do I extend you?" in any chat,
  Skales now answers with the real routes above instead of guessing at ports
  and reverse-engineering. The one honest limit is stated too: no injecting
  panels into the Skales window itself; a companion window wired over the
  connector and the remote API is the way.

- **A Debian package.** Debian, Ubuntu and Mint have a proper `.deb` again,
  next to the AppImage that runs on any distro. It is the one to take on
  Ubuntu 24.04 and newer: installing it keeps the Chromium sandbox switched on,
  where the AppImage has to fall back without it.

### Changed

- **Home and Work reach every theme.** Obsidian, Snowfield and Neon had no way
  to switch sides, so the sidebar was stuck on whichever one another theme had
  left behind. All three carry the switch now and group their entries by it,
  under the same headings as the full sidebar.

- **Codework and Swarm start switched off.** Both are a deliberate step rather
  than something to meet on the first launch: one runs commands and writes
  files in a real project folder, the other sends one task to several models at
  once. Turn either on in Add-Ons whenever you want it. An existing install
  keeps exactly what it had. Swarm also moves out of Main and down to Build,
  under WordPress, with the rest of the ways to run a job.

### Fixed

- **An answered question stays answered.** The occasional question Skales asks
  about how you work came back on the next reload, in full, as if you had never
  replied, sometimes days later with the old timestamp still under it. Your
  answer is kept with the message now. The same reload used to make the
  small "shape your agent" card disappear instead; it stays too.

- **Your accent colour reaches the rest of the app.** Around fifteen hundred
  buttons, links, icons, headings, rings and glows were painted in the shipped
  lime by hand and never moved when you picked a colour. They follow the accent
  now, including the tinted glass behind the panels, which takes on your hue
  while keeping its own depth. Green that means something, like a success tick
  or an online dot, stays green.

- **The Skales lettering is the logo again.** Under Obsidian it came out sand,
  under Snowfield blue, under Neon cyan, and elsewhere it followed whatever
  accent you had picked. The wordmark is the brand and no longer takes a colour
  from anything.

- **The stripe on the active sidebar button.** The pill in the default theme
  had a hard line down its left edge in a colour from the far end of its own
  gradient. It is one smooth pill now.

- **The accent belongs to Skales-X.** The other five themes are finished
  designs with an accent chosen against their own palette, and half of each
  stayed behind when a picked colour was walked through it. They keep their own
  accent; the picker says where it lives.

- **A coding session without an agent runs again.** "No agent" is the Code
  window's default for a reason: no persona, no extra prompt, fewer tokens.
  A session started on that default aborted immediately with "Isolated agent
  could not be resolved", and only picking a real agent got it moving. The
  default works now, and sessions broken this way heal on their next message.

- **Code stays in the Code window.** A run in the standalone Code window used
  to follow you into the app: clicking Chat landed you inside the coding
  session, its approval card and live stream included, and the coding session
  sat in the chat History as if it were a conversation. Code sessions now live
  with Code: the chat page, its recents, History and the command palette leave
  them out, and a finished coding run notifies with "Open Code" instead of
  posing as a chat reply.

- **The type size buttons in the Code settings size the type.** Every size in
  the window scales with the chosen number; before, the family changed and the
  size did nothing.

- **The composer's toolbar scrolls instead of wrapping.** The row under the
  input keeps one line at any width; a mouse wheel now scrolls it sideways,
  alongside the drag and trackpad swipe it already had.

## v12.5.71 - Windows You Can See Through

Two corrections to 12.5.7. Both are things you look at every day, and both were
caused by a rule that was right for the windows it was written for and wrong for
the ones it also reached.

### Fixed

- **Buddy is see-through again.** 12.5.7 gave each window the right to colour its
  own title bar in the current theme, so Flow and Code stop showing a dark
  caption bar in a light theme. Two windows have no title bar and no background
  at all - the desktop Buddy and the AIPointer overlay - and they were filled in
  along with the rest: a solid rectangle in the theme colour, sitting on your
  desktop. A window whose pixels come from the page is now left alone, and Flow
  and Code keep the caption bar that matches their theme.

- **The Home | Work switch obeys the click, not the page you are on.** The
  sidebar follows you: land on a page only one side shows, and it turns to that
  side so the page you are on is always in the menu. That rule was also
  answering your own switch. On Discover - a Home page - pressing Work set the
  side and the rule put it straight back, so the only way from Discover to Code
  was a detour via a page both sides show. The rule now answers a move between
  pages, once. Standing still, the choice is yours.

## v12.5.7 - Code Gets Its Own Window

Code stops being a page inside Skales and becomes a program of its own: a
window built for working on a repository, built for that job rather than for
a conversation. Point it at a folder, watch it read, plan and
ship the diff, review every change beside the transcript, and tell it - once,
globally, or per session - how you like to work. It runs the same engine as
everything else and none of the identity: a coding agent, and nothing else.

### Added

- **Hugging Face works the way it looks like it works.** The token test can
  actually fail now - it checks who you are and then makes one real call, so a
  made-up token stops passing - and a rejected token says which permission it is
  missing, with a link straight to the page that sets it. Activating a Space
  reads that Space's own API instead of guessing at it, and when a Space will not
  answer, the card says the shape was guessed rather than pretending otherwise;
  you can correct the endpoint, the inputs and the output type by hand, or have
  the API read again. Spaces built on current Gradio work, not only the old
  ones. A Space that returns a file returns a picture, a video or a sound you can
  see, saved into your gallery rather than handed to you as a link that expires.
  Speech-to-text and text-to-speech can both run through Hugging Face. Embeddings
  work. Music is no longer one single model. A dedicated endpoint is tested as
  the kind of endpoint it is instead of always as a chat. And the model catalogue
  is read properly at last: prices, context sizes, tool support and the real
  providers behind a model, instead of a list of the people who uploaded it.

- **Remote MCP servers speak the current protocol.** Skales now offers the
  2025-06-18 revision and falls back to 2024-11-05 for a server that has not
  moved yet, so servers that had begun refusing the older revision connect again.

- **Skales Code has a terminal, and it is yours.** A real one, under the box you
  type in and dragged to whatever height you want: your login shell, in the
  session's folder, with your profile. Not a command field, so vim, top, an
  interactive prompt, colours, arrow keys and Ctrl-C all behave the way they do
  in a terminal. Several tabs if you want them, a switch in the title bar, and
  /terminal. It is yours in the sense that matters: the agent can neither see it
  nor type into it, so it needs none of the agent's rules, and what the agent
  runs has its own place further down.

- **A command that never ends can run in the background.** A dev server, a
  watcher, a long build used to be a choice between a timeout and a blocked
  session. Now it starts, hands back a handle, and keeps going: its output
  appears under the step that started it and grows while you watch, and a strip
  above the status line lists everything that is running with what it is, how
  long it has been going, and a stop button. Nothing limits how many run and
  nothing times them out. Stopping really stops it, the whole tree, so a port
  actually comes free. A session that ends ends its own, and so does closing
  Skales. Your phone can see the same list and press the same stop.

- **Call any API from Skales.** A tool for talking to a REST API or submitting a
  form: your method, your headers, your body, the answer back. A form-shaped
  body is sent as a form, which is the difference between a request that works
  and one that quietly does nothing. Reading runs, writing asks first. Your own
  network is reachable, a NAS or a second machine or a service in your tailnet;
  this computer itself is not, because that is where Skales listens, and a page
  or a document could otherwise talk it into calling the app. A switch under
  Security opens that too, and says why it is shut.

- **A template for Code opens Code.** Clicking a coding template used to open Lio
  AI, and the card said so. It opens Skales Code now, with the prompt already in
  the box. Five new ones for the work a developer actually brings: find and fix
  this bug, review my current changes, explain this codebase to me, refactor this
  safely, find what is untested and test it. Each of them insists on running the
  thing rather than claiming it.

- **The instructions box shows what belongs in it.** The place where you tell
  every coding session how you work was an empty field with no example. It now
  hints at the four things that earn their place there: the language to answer
  in, your own test command, your commit style, your no-gos. A hint, not a
  filled-in file: rules nobody wrote should not ride along invisibly.

- **Working in Code shows up in Discover.** The biggest thing in this release had
  no place in the feed at all. A coding session, a change shipped out of the
  window and a run with several agents now do, in the same content-free shape
  the Studio entries have: never a repository, a branch, a path, a commit message
  or a file count. The feed is public and the work is private.

- **Code is its own program now.** Clicking Code opens a window that looks and
  behaves like a tool for writing software, not like a chat with a folder
  attached. Its own title bar, its own type, its own light and dark, and a
  status line along the bottom that always says what is going on: idle or
  working, which folder, which branch, and how many lines it has added and
  removed. Point it at a repository and say what you want changed. You can open
  a project on this computer or clone one from an address, an ssh address
  included, and the sessions you had open before are listed underneath with the
  branch and the size of the change in each. Four ways to work: Ask reads and answers
  and changes nothing, Code makes the change, Plan writes out what it would do
  first, Auto gets on with it. Pick the model and how hard it should think right
  there in the box you type into.

- **A coding session reads like a log, not like a conversation.** The Code
  window shows the whole session in one column: what you asked, what it
  answered, and every step in between as its own line. Read, Grep, Edit, Bash,
  with the file or the command beside it and how many lines it added and
  removed. An edit shows the change right there, the lines it replaced in red
  and the new ones in green, with the line numbers from the file. A command
  shows its output in a terminal block underneath. The checklist it is working
  through updates in place, with the finished items struck out. It fills in as
  it happens rather than all at once at the end, and scrolling up to read
  something earlier no longer drags you back to the bottom every second.

- **Your coding sessions, down the side.** Code has a column you can open from
  the title bar: a new session at the top, a search that looks at both the name
  and the repository, and everything you have worked on grouped into today and
  earlier, with the repository and what changed under each. Right-click one to
  rename it, put it away, duplicate it, download the whole session as a
  readable file, or delete it. A duplicate brings the conversation with it, so
  you can try a second approach without losing the first, and the download
  includes the diffs, because a record of a coding session without the changes
  in it is a record of nothing. A new session is named after the first thing
  you ask it rather than after the folder, which the row already tells you.

- **Review what changed, then ship it, without leaving the window.** Open the
  review panel from the title bar and the changed files sit beside the
  transcript. Pick one and you get it three ways: the change itself with line
  numbers, the file as it now reads, and, for something worth looking at like a
  markdown file or an image, a real preview. Keep it or put it back, write a
  commit line, and open a pull request: the branch is pushed and the request is
  created for you, with GitHub's own words back if it refuses. It needs the
  GitHub command-line tool signed in, and says so plainly when it is not there
  instead of just failing. Underneath, everything this session changed with a
  way to undo any single one of them, and the repository's other checkouts.

- **Point at a file, drop in a screenshot, or just type a command.** In Code,
  typing @ offers the files of the repository you are in, matched on the whole
  path so a folder name finds them, and the file goes along with your message
  so it does not have to go looking. Drop files onto the box, paste a
  screenshot, or use the paperclip: video, audio, PDFs and archives all attach
  the way they do in a chat, with the chat's limits and no new ones, and they
  show as chips above what you are typing, with their size and a thumbnail for
  a picture. A slash at the
  start of the line opens the short list of things a coding session can do:
  switch how it works, open the review panel, commit, open a pull request, stop
  it, start a new session. A slash anywhere else is just a path, and stays one.

- **When it stops to ask, the question is right there.** A question from Code
  now appears in the session itself, with its options numbered so you can
  answer with a keypress, or write something else entirely in the box below.
  When a tool wants permission, the same: what it wants to run, and Allow once,
  Deny, or stop asking about that tool for this session. While either is open
  the header and the bottom line both say it is waiting for you, and the box
  says so too instead of quietly doing nothing. A question you already answered
  stays readable but can no longer be clicked, so an old card cannot answer a
  question the session has long moved past.

- **The status line now counts the context, and a picture you sent shows as a
  picture.** The bottom line of the Code window shows how much of the model's
  window the conversation occupies. A message that carried screenshots or files
  shows their names as small chips instead of raw text, an agent's log can be
  opened from its card, the review panel says when a very long change was cut
  short instead of ending silently, and a message typed in the last moment of a
  run is carried into a fresh run instead of quietly disappearing.

- **Answers read the way they were written.** Text from Code renders properly
  now, so a filename in code marks, a bulleted list of findings or a snippet
  comes out looking like what it is, rather than showing you the asterisks.

- **Watch several agents work at once, and see what they cost.** When a coding
  session splits a job across parallel agents, Code shows them down the right:
  what each was asked to do, how long it has been going, how many tools it has
  used, and its answer when it has one. Underneath, what the run has spent, split
  between the main conversation and the agents, with a total. Stop all is in the
  header while any of them is running. A figure that was never recorded says so
  rather than showing a zero that would read as free, and from now on those
  figures are recorded, including for a run that spent them and then failed.

- **Code is a coding agent, and nothing else.** A session in the Code window no
  longer carries Skales' personality, its interests, or anything it remembers
  about you. It is told where it is, what it may change, which shell this
  machine runs, and how to work, and that is all. Measured on a real request:
  the instructions it sends dropped from about 13,800 characters to about
  4,200, which is roughly seventy per cent less on every single turn. It keeps
  the whole toolset, because a coding agent with fewer tools is a worse one;
  what it loses is the handful that read or write your memory, refused both in
  what it is offered and in what it may run.

- **Tell it how you like to work, once.** The gear in the Code window holds
  instructions that apply to every coding session, here and behind the /code
  command in chat, plus instructions for just this session when one job needs
  something different. Alongside them it shows what the project itself asks for
  in its own CLAUDE.md or AGENTS.md, read-only, because that file belongs to
  the repository. The more specific one wins. The gear also holds light and
  dark for this window, and follows the system if you would rather it did.

- **A phone will be able to tell a coding session from a chat.** The session
  list Skales sends to a paired phone now carries, for a coding session, the
  folder it is in, how it is working, whether it is running or waiting for you,
  and how much it has changed. Nothing on the phone uses it yet; it is there so
  that when it does, it draws the same row this Mac draws instead of a second
  version of it.

- **It hears you while it is working.** Type while a session is running and the
  message waits in line instead of being refused; it is picked up at the next
  step and the count of what is waiting is on screen the whole time. Everything
  the window can do it does through the same channel a phone will later use, so
  the same session can be watched and steered from somewhere else without any
  of it being built twice.

- **Code sessions show up again on a machine you have been using a while.** The
  list of coding sessions was empty for anyone whose conversation list had been
  built before Code existed, because the saved list did not know what a bound
  folder was. It is rebuilt once, and they are all there.

- **Code moved up, and can be put away.** Code now sits in the main part of the
  sidebar, directly above Studio, where the work you do every day lives. And
  like Studio it is an add-on: if you do not write code, switch it off under
  Add-Ons and the entry is gone. It is on to begin with.

- **Code: a place to work in a folder.** A new entry in the sidebar opens Skales
  on a folder of your choosing, in its own window, so you can keep chatting and
  using everything else while it works there. It lists the folders you have
  worked in before, and you pick how it should behave: Code changes files and
  asks before anything risky, Plan only reads and shows you what it would do,
  Auto gets on with it. It is the same engine that has been behind the /code
  command in chat, with a surface of its own. Pick a session and you see the
  repository it sits in: the branch, how far ahead or behind it is, every
  changed file, and a box to commit them. A folder that is not a repository
  says so rather than showing an error, and when git refuses a commit you get
  git's own words back, not a generic failure. Click any changed file and you
  see what changed in it, with Keep and Put back: Keep stages it for the next
  commit, Put back restores the file the way it was and tells you where it took
  the old version from. A brand new file shows its contents instead of an empty
  comparison. Alongside it there is a list of every file this session wrote,
  changed or deleted, each with its own undo, and the ones you already undid
  stay in the list so the record stays complete. And you can work on two things
  at once: give a second piece of work a name and Skales checks the repository
  out again in a folder of its own, on a branch of its own, with its own
  session, so neither run can overwrite the other. Each session says whether it
  is working, waiting for an answer from you, or idle. Removing a parallel
  checkout tells you when there is uncommitted work in it rather than taking it
  with the folder, and it will never remove the repository itself.

- **Your accent colours stop at the logo.** Custom accents recolour buttons,
  links, glows and selections; the Skales lettering in the sidebar keeps its own
  colours in every theme, because that is the brand, not a control.

- **Your own accent colours.** Under Appearance you can now set the three
  colours the Skales gradient is made of, and everything drawn in the accent
  follows: buttons, links, the glow, the selected row, the wordmark, the command
  token in the composer. Skales adjusts the brightness where it has to so the
  result stays readable on the page you are on, tells you when it did and by how
  much, and one click puts the original colours back. The window frame of the
  Flow and Code windows now follows the theme as well, instead of staying dark.

- **A team plan shows who waits for whom.** The team leader has always decided
  which subtasks can run at once and which have to wait for earlier results, and
  the run has always followed that - but the plan was printed as a flat list, so
  five agents working in parallel looked exactly like five agents working in a
  chain. The plan is now drawn in stages, each stage saying how many run
  together, and a subtask that waits names what it waits for.

- **Skales says when it shortens a long conversation.** To keep a long chat
  inside the model's limit, Skales summarises the older part of it. That has
  always happened quietly, so from the outside it just looked like the assistant
  starting to forget things. It now says so in the conversation, with what was
  shortened, and makes clear that nothing was deleted.

- **Sub-agents get a role instead of everything.** When Skales splits a job into
  parallel sub-agents, each of them was handed the complete tool set: a
  sub-agent researching competitors could send WhatsApp messages, write to your
  long-term memory, stop to ask you a question, and start sub-agents of its own
  without end. Three of those are now impossible for a sub-agent whatever else
  changes, and a sub-agent that needs a capability it was not given can ask for
  it mid-job and carry on. The narrower tool sets themselves are off by default
  for now and can be switched on in settings; while they are off, every request
  a sub-agent makes is recorded, so the defaults are chosen from what jobs
  actually needed rather than from a guess.

- **Your own speech server, for reading aloud and for dictation.** There was a
  field for a custom text-to-speech address, and nothing in Skales ever read it:
  it was saved, carried along and backed up, and it never produced a sound.
  Settings now has a Custom voice provider with the four things such an endpoint
  actually needs, an address, a model, a voice and an optional key, and the same
  for transcription. Both are used before any cloud service, because a server
  you set up yourself is the one you meant. An address you had already entered
  in the old field starts working on its own, and if your endpoint does not
  answer, Skales says so instead of quietly reading in a different voice from
  somewhere else.

- **Folders Skales must never touch, whatever else you allow.** Until now the
  only way to limit file access was to list what Skales *may* reach, which is no
  help if you run it with Full Access on purpose. There is now a list of folders
  it must stay out of, and that list is not lifted by Full Access or by
  Unrestricted mode. It covers the file tools, the shell and scripts, search,
  and directory listings, so a blocked folder is not just unreadable, it is
  invisible; it cannot be picked as a project folder either. Shortcuts pointing
  into a blocked folder are followed before the check, so they are not a way
  around it. One button blocks Skales' own program folder without you typing a
  path, which also means it keeps working after you move the app.

- **Every step in a coding session opens.** A line in the Code window is no
  longer something to read past. Click a Read, a Grep, an Edit or a command and
  it unfolds: exactly what was sent, and everything that came back, whole,
  scrollable and with a copy button on each half. The line itself now also says
  how long the step took, so a slow turn shows you where the time went.

- **A change decides itself where you see it.** The diff under an edit carries
  its own added and removed counts and its own Keep and Revert, so reviewing no
  longer means finding the same file again in the panel.

- **Every file is a link.** A path in a tool line, a path an answer mentions in
  backticks, a file in a diff: click it and it opens in the panel beside the
  transcript. That includes the Markdown ones.

- **Something to do with a turn.** Under every answer: copy it, have it read out
  loud, fork a new session from that point, or roll the whole session back to
  it, files included. Under your own turns: change what you asked and send it
  again. Your own words now sit on the right, in a block of their own.

- **A page you wrote, running beside the transcript.** Click an HTML file in a
  coding session and the panel does not show you its source, it shows you the
  page: its own stylesheet and script load with it, so a widget or a small app
  works there the way it will work anywhere. Markdown is rendered, images are
  shown, and everything else is now coloured instead of one grey block.

- **Point at something in a running page.** Turn on the picker, hover the page,
  click what you mean, and the element goes onto the message you are writing:
  what to call it in CSS, its own markup, and a picture of just that piece. So
  "change this one" is a thing you point at rather than describe.

- **Chat or Split, from the title bar.** The panel used to be reachable only
  when git had noticed a change, which has nothing to do with wanting to look at
  a file. Now it is a switch, and it is there whenever a session is.

- **Hand a coding session over to Code.** A chat that is bound to a folder and
  running in a code mode gets one button in its header: it stops what is
  running, cleanly, and opens that same session in the Code window. Not a link
  and not a second copy. The same session, in the window built for it, with
  your chat left exactly where it was.

- **The landing says something different every time.** Twenty-two greetings, in
  every language, in rotation rather than at random, and the headline flickers
  for a moment every few seconds. It holds still for anyone who has asked their
  computer for less motion.

- **Dictate into the box.** A microphone in the composer, running the same
  speech-to-text everything else in Skales runs. What it hears joins what you
  already typed instead of replacing it.

- **Which agent runs the session, next to the model.** By default: none. An
  agent carries a system prompt and a persona, and a coding session should have
  neither unless you deliberately picked one. If you do pick one, it is that
  agent's prompt from the next turn.

- **The Skills button says what else is connected.** Which MCP servers are on
  and how many tools each one brings, and which connectors are set up, instead
  of leaving you to wonder whether any of it reached this window. A step that
  went through an MCP server now says which one.

- **A message you send from your phone while it is working joins the queue**
  instead of starting a second turn on the same session.

- **Two files for your standing rules, not one.** SKALES.md is still how you
  want to be answered, everywhere. AGENTS.md is new and is about code only: the
  style, the checks to run, the folders never to touch. One editor in Settings,
  a switch above it, and both are ordinary files you can open in a text editor.
  A project's own CLAUDE.md still wins for that project.

- **Look at what you published.** Two new tools alongside the FTP upload that
  has been there for years: list a directory on your server, and download a file
  from it. Same saved profiles, all three protocols, and an agent working on its
  own website can finally read its own files back.

- **Every coding session gets a scratch folder of its own.** Somewhere to put a
  captured log or a half-finished file that is not part of your repository and
  should never show up in your diff. It is emptied when you clear the session and
  deleted when you delete it.

- **`/clear` in Code.** Empties the session and its scratch folder and keeps the
  repository, because starting again almost never means starting somewhere else.

- **The sidebar has two sides now: Home and Work.** A switch under the provider
  status, and the menu regroups: Home leads with Chat, History, Discover, Memory
  and Lio AI and keeps Browser, Group Chat, Spaces and Wrapped together; Work
  leads with Code, Organization, Autopilot, Teams and Swarm and puts Codework,
  Browser, Playbooks, Workflow and WordPress under Build. Workspace, your own
  widgets and System stand in both. Your choice is remembered, and collapsed the
  switch becomes one button showing the side you are on.

- **Skales asks once how you want to use it.** A single question after the
  privacy notice, with two equal answers, and all it decides is which half of
  the menu you see first. Skippable, and changeable any time.

- **Code pulses once, quietly, if you have not opened it yet.** It stops the
  first time you click it and does not come back in the next release.

- **Hand a design to a coding session.** The paperclip in Code now offers a
  Studio or Flow project as well as files: pick one and the session gets a
  snapshot of it, the markup, a list of the files it is made of, and a picture
  of how it looks. Read-only, so the session can match it and never write into
  it. So the thing you designed and the thing you are building can finally see
  each other without an export in between.

- **No emoji in Code, at all.** Where a step or an answer arrives carrying a
  tick, a cross or a warning sign, the window draws its own icon and keeps the
  meaning. Everything else is removed, and Code is told not to put emoji in the
  files it writes either.

- **Code follows Skales, not your Mac.** Light stays light and dark stays dark
  because of what Skales is set to, and it changes the moment you change it,
  even with the Code window open beside the main one.

- **Skales Code has a browser, and the project's files beside it.** A page the
  agent just wrote, a dev server it just started, a file you want to look at:
  there is a panel for that now, next to the transcript rather than instead of
  it, with an address bar, back and forward, reload and open-in-your-real-
  browser. The file column lists what is in the folder and opens it with a
  click. Its own panel, because "what changed" and "what does it look like" are
  two questions and the review panel only answers the first.

- **A page printed in the answer can be run where it stands.** When Code writes
  a whole HTML page and shows it to you, the block carries two tabs: the source,
  which is still what opens, and the page itself, running. No copying it out to
  a file to find out whether it looks right.

- **The panels on the right can be dragged wider.** Both of them, from their
  left edge, with the mouse or with the arrow keys, and each window remembers
  what you set.

- **New coding sessions start on what you choose.** The gear in Code now holds
  the defaults: which model a new session opens on, which agent runs it, and
  which working mode it starts in. Nothing there touches a session that is
  already open. Beside them, how the window reads - the typeface and the size -
  which stays on this machine.

- **Code says what it is allowed to do.** The settings panel now states, in
  words, that a coding session runs on the working mode you pick in it and not
  on the safety mode in Settings, what each mode asks about, and the five things
  no mode lifts: the folder is never left, Skales' own files are never
  writable, commands that cannot be undone are refused, and a coding session can
  neither read nor write your memory.

- **Skales itself can run a coding session.** The agent list in Code offered
  every agent on the machine except the one the chat window runs on. It is
  there now, second in the list, for when you want Skales with its memory on a
  repository rather than the lean coding identity.

### Changed

- **All eighteen AI providers are on the providers page, not four.** OpenAI,
  Anthropic, Google, Groq, Mistral, DeepSeek, xAI, Together, Minimax, Moonshot,
  GLM, Qwen, Cloudflare and Nvidia were folded into a collapsed "More LLMs"
  section, so the one page that answers what Skales can talk to showed you an
  aggregator, a local runner, a text field and a router. They are all listed
  now, in a sensible order, and the "show only active" checkbox above the list
  is there when you want it short. The cards themselves used to be built twice,
  in two places that had drifted apart three times in past releases; they are
  built once now, so a button can no longer exist for half the providers.

- **Lio AI now has an address that matches its name.** The sidebar entry read
  "Lio AI" while the page it opened lived at /code, so links, bookmarks and the
  assistant's own answers about where things are all pointed somewhere that
  said something else. Lio AI is at /lio from now on. An old /code link still
  takes you there, so nothing you saved stops working.

- **Code asks you, not your safety mode.** The leash is the mode you picked when
  the session started: Ask and Plan read, Code asks before anything risky, Accept
  edits applies file changes and still asks for the shell, Auto runs. Set the
  rest of Skales to Unrestricted and Code still asks, because that is what
  choosing Code meant. Nothing that is not a question changes: the blocked
  folders, staying inside the bound folder, the command block and the shell rules
  were never approvals and are never lifted.

### Fixed

- **A mailbox that refuses your password by design now says which code to get.**
  QQ never accepts an account password over IMAP, it issues a separate
  authorisation code, and Skales answered "verify credentials", which sends you
  to check the one thing that was already right. QQ now gets the sentence that
  helps, in the words its own settings use, and so do 163 and 126, GMX, Web.de,
  iCloud, Zoho and Yandex: each names what to get and where. A provider that is
  not on the list at least learns that this is a thing that happens. And a
  failure that arrived as one word, "AggregateError", now says what actually
  went wrong on each address it tried.

- **The mail test button works before you save, and says what it does.** It was
  dead until the account was stored, which is backwards: testing is what you do
  before committing settings you are unsure about. It now checks what is in the
  form, and it says out loud that it only checks the login, sends nothing and
  changes nothing in your mailbox.

- **Searching a project no longer stops everything else.** Skales runs on one
  loop and every chat, poll and button shares it. A search across a source tree
  held it for a quarter of a second in one block and listing a project held it
  on every session open, so a running conversation stuttered for reasons that
  had nothing to do with it. Search, listing, reading and the project walk now
  give the loop back while they wait.

- **You can watch the browser work instead of seeing the pictures afterwards.**
  A screenshot only travelled with the finished tool result, so a browsing job
  of a dozen steps left the chat empty for a minute and then dropped every still
  in at once. The current picture now appears above the composer while the job
  runs and updates with each step, and it disappears when the run ends rather
  than sitting there looking live. The screenshots in the conversation are
  unchanged.

- **Skales can offer to turn a long answer into a goal, and it asks first.**
  The automatic goal start could only judge a message before it ran, so a short
  request that turned into fourteen open steps and thirty tool calls was never
  recognised as the bigger job it became. When a finished turn ends with several
  open steps, a lot of tool work, or an unfinished checklist, a line above the
  composer offers to carry it on as a goal. It never starts by itself: one click
  runs it, one click makes it go away, and doing nothing leaves the chat exactly
  as it was. When you have switched on the automatic start, a promoted message
  now says so in the conversation instead of only in a log, and neither half of
  this runs inside an isolated agent.

- **A team task that stops moving ends, and tells you where it stopped.** If a
  step never came back, the task stayed on "running" for as long as the app was
  open, and the log only listed which tools had been called, never what came of
  them. A step that produces nothing for six minutes is now given up on and
  marked as stopped, which is its own outcome and reads differently from a step
  that failed. The log lines carry the result of each tool, and a failed one
  says what went wrong right there in the timeline. The clock restarts on every
  answer, so a long task that keeps working is not interrupted.

- **The updater cannot install a version that is no longer the current one.**
  It checked for updates every four hours and then treated that answer as
  settled: the download took whatever the last check had named, and the install
  checked nothing at all beyond the file being there. On release day that meant
  two machines installed the previous version half an hour after the new one had
  gone out. Skales now re-reads the release feed before it downloads and again
  before it installs, and if the download has been overtaken it says so and
  offers the current one instead of installing the old one. When the feed cannot
  be reached, an already-downloaded and verified update still installs, and the
  log says it could not be re-checked rather than pretending it was.

- **A team task keeps its files together instead of on your Desktop.** The
  agents in an Execution Team were never given a place to work, and their
  instructions literally told them to save to the Desktop or the home folder,
  which in unrestricted mode is where the files ended up. Each run now has its
  own folder under the workspace, the agents are told to write there, and the
  run says which folder it is before it starts, so you can find the result.

- **Flow keeps its Render button after the first render, and you can start one
  from the chat.** Once a Motion project had been rendered, the button was gone:
  the preview had switched to the finished video, and the button only appeared
  while an HTML composition was on screen. It belongs to the project now, not to
  whatever file you happen to be looking at, and clicking it puts the
  composition back in the preview so you see what is being rendered. When it
  cannot run, it stays visible and greyed out with the reason on it, including
  on a machine where FFmpeg is not installed, instead of quietly not being
  there. The same action is now on the file card in the conversation, where the
  round that produced the composition is.

- **The Execution Team room is visible in a light theme.** The scene was drawn
  in white on the assumption of a dark room, so in Snowfield, or in any theme
  set to light, the desks, the people, the monitors and the names were white on
  white and the panel looked empty while a team was working in it. It is drawn
  in your theme's colours now and follows a theme switch immediately. The same
  thing hid your own dot on the Discover network map; it shows up too.

- **"Fetch Available Models" works on providers where it never could.** The
  button asked the provider directly from the window you were looking at, and a
  browser refuses that unless the provider explicitly allows it. Most do not, so
  the request never left your machine and the card reported a failure on a key
  that was fine. Nvidia NIM was where it was noticed; it was true for a dozen
  providers. The request now goes out from Skales itself, and when a provider
  does refuse, the card shows you what the provider said instead of a generic
  error. The model picker in chat was quietly affected by the same thing: it
  promised your actual models and fell back to a fixed list without saying so.

- **The Debug Panel works again on all five of its tabs.** Memory, Sessions,
  Tools and Status all answered with an authorization error, because the panel
  called the developer API with a token it had guessed rather than the one in
  your own DevKit config. Only the Adaptive tab worked, and only because it was
  the one tab that read the app directly. All five read the app directly now, so
  the panel shows you your machine whether or not the developer API is switched
  on. The API Playground stops guessing too: it fills the token field from your
  config, and says so plainly when there is no token to fill it with.

- **Settings no longer shows you the name of a text instead of the text.**
  Four provider cards printed something like settings.providers.nvidia_nim.desc
  where their description belongs, and all six theme cards in Appearance showed
  the word "Appearance" as their description. A few other places did the same
  thing: the standing instructions panel, the day picker in Planner, the refresh
  button on the Autopilot board, and four of the twelve voice languages in
  Studio. All of them now say what they mean, in all twelve languages, and a
  check now fails the build if a text is referenced that no language actually
  has, so this cannot come back unnoticed.

- **The desktop buddy now wears the theme you picked, and its bubble can no
  longer be cut off.** Four things were wrong with that little window at once.
  It ignored the theme system completely: it carried its own fixed set of
  colours and reduced every theme you can choose to a single light-or-dark
  guess, refreshed at most twice a minute, so it never matched the app and
  lagged behind a theme switch. It now reads the same colours as everything
  else and follows a change the moment you make it. In a light theme the
  "Open Chat" link was white text on a white bubble - invisible until you
  hovered it, which made it whiter; the speech tail stayed dark under a light
  bubble too. Both follow the theme now. The action button on a notification
  ("View Tasks", "Open calendar") did nothing at all when clicked; it now opens
  that page in the main window, and a notification without a page opens the
  chat rather than nothing. And the window itself was a fixed height that could
  not grow, with the page hiding anything that did not fit - a long answer was
  not scrolled, it was cut. The window now grows to fit what it has to say,
  upward from wherever you parked it, never past the edge of your screen, and
  goes back to its normal size when the bubble is gone. If a message is longer
  than the screen can hold, the bubble scrolls instead of hiding the rest.

- **A chat that sends out sub-agents now shows them working while they work.**
  Handing a job to parallel sub-agents puts a live status card under the message
  that started it, one chip per agent. That card only turned up after leaving the
  chat and coming back, or after a reload, so a job that was very much running
  looked like nothing had happened. The launch signal never reached the open
  chat: it travels on the same internal channel as the proactive dashboard
  notes, that channel is read-once, and a single unreadable item made the read
  throw away the entire batch. The signal was gone before anything could act on
  it. The channel now hands over exactly what it promises, and one bad message
  can no longer take the rest down with it, so the card shows up within seconds
  and its chips move as the agents finish. Two more things that ride the same
  channel come back with it: the "job finished" notice with its per-agent
  report, and proactive dashboard messages appearing in chat. And a job sent off
  in the very first turn of an empty chat, where there is no message yet to hang
  the card on, now shows it above the input instead of nowhere at all.

- **A click in the built-in browser no longer gives up because the page redrew
  itself.** Reading a page marks every element so the agent can act on it
  exactly, but a site that rebuilds itself while it works - most modern ones do,
  constantly - throws those marks away, and the click then failed on everything,
  including a site's own logo, while typing carried on working. Skales now also
  remembers what each element WAS, its role and its label, which survive a
  redraw, and falls back to finding it that way. If even that misses, the
  remembered label is handed to the two strategies that were already there and
  were never reached before, instead of the attempt ending.

- **The browser window that opens by itself now says whose it is.** A visible
  browsing run opens a real browser window next to Skales, and then it starts
  clicking and typing on its own. Nothing said where it came from, so closing it
  was the obvious reaction - which ends the run. Skales now says, once, that the
  window is its own doing, that it will move by itself, and that it should be
  left alone until it finishes. The sign-in flow says the same, and now also
  points at the chat for telling Skales you are done, rather than leaving people
  hunting for a button in Settings.

- **"Flow finished" opens Flow instead of taking over the window you are in.**
  Flow runs in a window of its own, but the notification's button navigated the
  current window, so the main Skales window turned into the Flow workspace.
  It now opens or brings forward the Flow window, the way the Studio tab
  already did.

- **A rendered video no longer reports a check that never ran.** Validating an
  MP4 needs ffprobe, which was looked up on the system path - and an app started
  from the Dock does not inherit the path from your terminal, so it was
  regularly not found. The check then fell back to "the file is not empty" and
  still showed a green Validated, next to a resolution of 0x0, a length of 0.0s
  and 0 fps. Skales now looks for ffprobe where it looks for ffmpeg, including
  the places a manual install puts it, and when it genuinely cannot be found the
  card says the file was only measured by size instead of claiming a verdict.

- **The settings panel in Code stopped loading and started answering.** It sat
  on "Loading..." forever with nothing loading, because the instructions could
  not be read at all. They can be read now.

- **Tooltips in Code are no longer cut off.** The ones on the title bar opened
  upwards, out of the window, and the ones by the box you type in were painted
  over by the bar beneath them. They are drawn above everything now and nudged
  back on screen at the edges.

- **Code is quiet.** Every reply used to raise a notification and play the
  chime, in the coding window, about the coding window. Worse, the button on it
  navigated that window away to the chat and took the session you were watching
  with it. Notifications belong to the main window; Code does its work without
  interrupting you.

- **The agent list in Code shows four and scrolls.** It opened as a column of
  every agent you have, over the transcript you were choosing it for.

- **A model picked in the Code window is the model that runs.** The Code model
  in Settings quietly overrode it, so the picker showed one name while the run
  used another. The setting still applies where it should: to /code in chat, and
  to a session that has no model of its own.

- **A Discover mention obeys the box that says it should not arrive.** With
  notifications muted and both Discover types switched off, mentions and replies
  still filled the dashboard. They were arriving under a name nothing here
  recognised, so every Discover setting let them past. Three places read that
  list and all three go through one check now.

- **An emoji in Skales is Skales' emoji.** The agent faces in Code were drawn by
  the operating system, so the gecko looked like Apple's gecko. And a lookup
  fault meant most hearts, hands and weather fell back to the system everywhere
  in the app, not only there. Both are fixed, and the coding agent wears a
  laptop, which is a face Skales can actually draw.

- **The sidebar breathes evenly.** The gap under the provider box now matches
  the one above it, and with the sidebar collapsed the Home button is exactly as
  wide as the box above it.

- **Chat and History are in Work as well as Home**, in the same place, and the
  sidebar always shows the page you are on: opening Discover from a notification
  no longer leaves the menu with nothing marked.

## v12.5.6 - An Answer You Can Actually Give

Being asked a question is no use if the form has nowhere to put your answer.
Skales could ask you four things and accept none of them, and the setup screen
for browsing the web reported itself ready on the same page that said it was
not. Both were the same kind of fault: something reported a state instead of
checking it.

### Added

- **Skales can speak on your phone in a voice from this Mac.** This computer has
  dozens of voices installed, the premium ones included, and they cost nothing
  and never leave your own two devices. The paired app (2.5.6 and newer) offers
  them under Voice: pick one from the list this machine reports and it speaks
  for the phone over the pairing connection. Nothing is played on this
  computer's speakers - the phone asked for the audio, not for the room to hear
  it - and if this machine goes away mid-sentence the phone falls back to its
  own voice rather than going quiet. The same is available locally at
  /api/tts/say. A computer that is not a Mac says so instead of reporting an
  empty list of voices.

- **The Organization scene shows you who is stuck.** The office under a running
  team was decoration: wood-grained desks, a steaming mug, a bouncing ball you
  could kick. With six agents working, none of it told you which one had stalled
  without reading six labels. It has been redrawn around that one job. Colour
  now means state and nothing else - the furniture is near-monochrome and a pool
  of light on the floor under each desk carries the agent's status, readable
  across the whole panel before a single word is. The monitor is the status lamp,
  breathing while there is work and dim while there is not; the ring around an
  agent grows when the state changes instead of blinking, so a glance mid-change
  still reads correctly; and the chip under each desk shows the tool actually
  running rather than the word "Working". The mug, the bookshelf, the clock and
  the kickable ball are gone with it.

- **Instructions you write once, followed everywhere.** Telling Skales how you
  want it to work - answer in German, keep it short, never open with a
  compliment - lasted exactly one conversation, and the next chat started
  ignorant again. Code mode could read a project's own CLAUDE.md, but only with
  a folder bound, and that file is about a project rather than about you.
  Settings, under Chat & Code, now has a box for standing instructions. What you
  put in it travels with every conversation, with tasks and schedules that run
  while you are away, and with custom agents you built yourself. Two places
  leave it out deliberately: an isolated agent, which carries nothing of yours
  by design, and the internal calls Skales makes to itself, whose answers are
  read by the app rather than by you. A project's own CLAUDE.md is the more
  specific instruction and still wins for that project.
  The box is not the only way in. It is backed by a plain SKALES.md file in your
  data dir, the path is printed under the box, and editing it in a text editor
  is the same edit: the box re-reads the file whenever you come back to the
  window. If it changed on disk while you had unsaved text here, it says so and
  refuses to save over it, so the two ways of editing cannot quietly eat each
  other's work.

- **Right-click a key field and paste into it.** A password field in Settings
  gets its own menu - copy the value while it stays masked, reveal it, remove it
  - and the app suppresses the ordinary Cut/Paste one so the two do not draw on
  top of each other. Between the two, nothing offered to put a key *in*: the
  menu opened, showed three things you could do to a key you already had, and
  none of them helped when the key was sitting on your clipboard. Paste is in
  that menu now. It respects a selection, so pasting over a highlighted key
  replaces it, and the keyboard shortcut keeps working as it always did.

### Fixed

- **A page you visit cannot reach the Skales API on your own machine.** The
  local server is reachable from any page open in your browser, and that was
  being closed one route at a time: the same-origin check sat on 2 of 225
  routes. There is one shared check now, in front of every /api request, and it
  refuses exactly one thing - a caller that CLAIMS an origin which is neither
  this server nor your own machine, which is the shape a page on another site
  makes when it fetches your local port. Everything that is not a browser is
  untouched: the messaging bots, the relay, the CLI, the phone over your LAN and
  the app talking to itself all keep working, and each of them has a test. The
  image context menu had the same hole in miniature and no longer opens a menu
  for a message no frame on the page sent.
- **Discover stops passing its own template off as your model's work.**
  Composing a post gave the model 45 seconds, then quietly substituted a local
  phrase bank and returned it with no mark on it. With auto-post on, that
  template was published under your name as if your agent had written it. The
  ceiling is 180 seconds now - measured: at 45 seconds ten live models were
  being called dead, and at 180 seconds seven of them answered - the composer no
  longer gives up before the server does, a fallback draft says that it is one,
  and auto-post holds for it. Cloud versus local was also decided by a list of
  five provider names, so every provider added since counted as running on your
  own computer; the endpoint decides now.
- **The in-app guide is the guide the assistant reads.** The served copy of the
  guide, which is what fetch_skales_docs answers from, was five releases behind
  the source one while both showed the current version in their header. They are
  copied on every version stamp now, and the guide covers 12.5.1 through 12.5.6.
- **Muting notifications is now actually silent.** The switch promises no pings
  on any channel while everything still lands on the Notifications page, and
  those two halves were the leak. A notification is written to that page first
  and only then silenced, and the toast that pops up in the corner was being
  built from the page's own records - so muting stopped the browser
  notification and the messengers, and a toast and a sound came through anyway.
  In practice that meant a Briefing nudge and a Discover mention still
  interrupted you. The toast is a channel like the others now and falls silent
  with them. One exception stays, the same one the other channels make: a
  request that is waiting on your answer still reaches you, because silencing
  that would leave work stalled with nothing to show for it. What was silenced
  is not saved up either - unmuting does not empty the quiet period onto your
  screen; it is all on the Notifications page, where it always was.
- **A mention in the Discover feed stopped outranking your own settings.** It
  was filed at the urgency reserved for things that block work until you
  answer, which let it through Mute, through quiet hours, and past the limit
  you can set on how often Discover may ping - while still using up that
  limit's daily slot, so a mention could silence the normal feed ping. It is
  filed as ordinary now and obeys all three.

- **A question with choices no longer stops at the choices.** When Skales asked
  you something and offered buttons to answer it, the buttons were all there
  was. A question like "What is your name and what do you do?" came with four
  roles to pick from and no way to type a name, so the one answer being asked
  for could not be given - the card sat there, nothing moved, and the run looked
  like it had died. Every set of choices now ends with one you can write into
  yourself. The rest of the card caught up too: the short label a question
  carries is shown, and the line of explanation that comes with each choice -
  the part that tells you what picking it actually means - is shown under it
  instead of being dropped on the way to the screen. Questions asked before this
  release still open and still work.
- **Browser Control says whether it can browse, not whether it once could.**
  The setup screen read a note written the last time an install had gone well,
  and nothing ever revised it. So the panel could sit there marked ready while
  the same panel, a few lines further down, refused to open a window and said
  the browser was missing - and it was: the app had moved on to a newer browser
  build that had never been fetched. The screen now asks the question it was
  claiming to answer, every time you open it: would opening a page work right
  now, with the browser you have chosen. If it would not, it says which part is
  missing and offers the button that fixes it, instead of hiding that button
  because it believed everything was fine.
- **A failure to launch is no longer reported as a failure to install.** Any
  problem that so much as mentioned the browser engine was rewritten into "not
  installed", including the engine's own complaint that it could not find the
  browser it wanted. The advice attached to it sent you to the screen you were
  already looking at, to press a button that screen was hiding. Each of those is
  now named for what it is, and the advice names something you can do - fetch
  the browser, or switch to the one already on your machine.
- **Install Chromium can install Chromium.** The button had two ways to do
  nothing. With "Use full Chromium" turned on it took a shortcut meant for
  people using their own Chrome, reported success and downloaded nothing. And in
  the packaged app the downloader itself had not been shipped: the build keeps
  the parts of a library it can see being used, and the piece that fetches a
  browser is started in a way that keeps it out of sight, so the one build that
  needs the button most was the one where it failed. Both are fixed, and the
  button now confirms the result against a real launch rather than against its
  own exit code.
- **A privacy note stopped introducing itself twice.** The desktop screenshot
  notice began "Privacy: Privacy:", because the screen put a heading in front of
  a sentence that already carried its own - in whichever language you read it
  in. The heading the translation uses is now the only one shown.

- **The Nvidia model list is the models that answer.** Every entry was called
  once and checked: of the 118 the catalogue lists, 44 reply to an actual
  message. Three of the six that shipped had been retired - including the one
  labelled "(Best)", which is the first thing a new user reaches for, so their
  first message failed and read like the provider was down. The list is now the
  verified ones, fastest first, and two things it taught are carried with it: a
  model that appears in a catalogue is not the same as a model you can call, and
  a slow first token is not a dead model.
- **A retired model is reported as a model problem, not a setup problem.** Nvidia
  answers a request for a withdrawn model with the words "404 page not found" -
  no code, no JSON - and that was surfaced as though the connection or the key
  were wrong, sending you to check settings that were fine.
- **The curated model picks are the ones you actually see.** The list was filed
  under one name for the provider and looked up under another, so the app
  decided there was no curation and showed the first few hardcoded entries
  instead. Refreshing against 118 live models changed nothing on screen.
- **An empty "Allowed tools" field means every tool, not none.** Leaving the box
  blank on a custom agent saved an empty list, and an empty list is a complete
  answer: this agent may use nothing. So the one thing you do when you don't
  know the tool names gave the agent no tools at all, and it failed by politely
  explaining it had no capabilities. Blank now means what a person means by it.
  The field also stopped requiring you to know the answer in advance: it filters
  all 184 tools by name and description, shows what you picked as chips, and
  says so when a name matches nothing.
- **An isolated agent cannot reach the two tools that were a shell in disguise.**
  Running commands is denied to an isolated agent on purpose. Two other tools
  each took an optional command and passed it straight through without a list of
  what is allowed, which made either of them a full shell by another name. They
  are denied now and gone from that agent's catalogue. No isolated agent could
  reach them in practice, because both need a bound code folder - an accident of
  plumbing rather than a decision, and it would have stopped being true the
  moment one got a folder.
- **Video rendering works on Linux.** Every Linux build shipped a copy of the
  video encoder built for macOS, and it was the first one the app found, so
  rendering a Studio video or a Flow motion died at the moment it started the
  encoder. The app now refuses a binary that cannot run on the machine it is
  on - on any platform, not just macOS - and looks in the place the correct
  Linux build actually ships, falling back to one installed on the system and,
  failing that, saying plainly that it found none.

## v12.5.5 - Nothing You Made Is Gone

A turn used to be a one-way door. Regenerating an answer deleted the one you
had; a Flow turn rewrote the deck you liked and the previous version was gone
from the disk. Both keep their history now, and both let you walk back into it.
Around that: Stop actually stops, an uncapped task runs uncapped, an isolated
agent stops writing into your memory, and GLM and Qwen become providers you can
simply pick.

### Added

- **A crash screen now tells you what crashed.** When a page stopped drawing,
  all you got was a line like "Minified React error #482" and a link to look it
  up. The detail that actually identifies the fault was being collected and
  written straight to the browser console, which is no help at all on the phone
  where these crashes tend to happen. Both crash screens now show that detail on
  the page: a ready-made report with the error, the page, and where in the app
  it broke, in a box you can select, plus a copy button. If copying is blocked -
  which it is on a plain connection to your desktop - the button says to select
  the text instead of pretending it worked.
- **Flow projects can be put away or thrown out.** The project grid only ever
  grew: a run that went the wrong way, a project started by accident and a
  throwaway test all stayed in it for good, because nothing in Flow could remove
  one. Each card now has two actions. Archiving is reversible and changes
  nothing else - the project keeps its files, its versions and its chat, and
  only leaves the grid, with a switch in the toolbar to look through what you
  put away. Deleting is the one that removes work, so it does not simply erase:
  the project folder moves to a trash folder next to your projects and can be
  recovered by hand. What deleting really ends is the conversation, and the
  confirmation says so instead of asking whether you are sure.
- **Motion can be made in a technique, not just in a palette.** Asking for
  papercut, pixel art or particles used to get you a normal animation with those
  words in the copy: the composition could only move boxes and text around, so
  that is what came back. Motion now has a Style setting of its own next to the
  model and the template, with fourteen techniques - papercut, pixel, particles,
  whiteboard, kinetic type, motion graphics, sketch, ASCII, isometric,
  blueprint, neon, comic, origami and silhouette - and three effects that stack
  on any of them: stop motion, glitch and film grain. Each technique brings the
  drawing it needs rather than imitating it, so pixel art is really drawn on a
  pixel grid and particles really are a particle system. It is a separate choice
  from a style pack, which decides the colours and the type: pick both and you
  get your palette, made that way.
- **A rendered video is checked before you are told it is ready.** Flow could
  already look over a finished design, but for Motion it looked at the
  composition, never at the video that came out of it. So the faults that only
  exist in the file went out unseen: a clip that freezes on one frame halfway
  through, a stretch that comes out black, a video that never moves at all
  because the animation runs on the clock instead of on the frame being drawn.
  Every finished render is now read back frame by frame, and anything found goes
  straight back into the project as a correction, with the exact seconds it
  happens at. The checks that need no vision model run on every machine; where a
  vision model is set up, the frames that actually differ from one another are
  looked at as well, so a broken transition or clipped text is caught in the
  file rather than in the preview.
- **A Flow project keeps every version of its artifact.** Each turn's result is
  archived before the next one can overwrite it, up to the last twelve, and the
  history opens from the preview toolbar as real renders of each version rather
  than a list of dates. From a version you can go back to it, download it as a
  ZIP without disturbing what you have now, or branch it into a project of its
  own and try the other direction. Coming back is not a one-way door either: the
  state a rollback replaces is kept as a version too, and the chat is told what
  happened so the next turn works from the files that are actually there.
- **Flow can just do what you said.** Every mode hands the run a starting file,
  a catalogue of layouts and a checklist. That is what makes a one-line brief
  come out finished, and it is in the way when you already know exactly what you
  want. A "Free" switch next to the brief turns all of it off: your brief is the
  whole specification, and nothing else is added. What you chose yourself stays
  on - your files, your template, your Brand Kit, your style pack. Motion keeps
  its starting file, because that one is the format the video is rendered from,
  not a suggestion.
- **A video can be as long as you asked for.** The length was a row of fixed
  buttons, one of them always picked, and that pick beat anything the brief
  said - so asking for 45 seconds quietly got you 5. "Follow instructions" is
  now one of the choices, and with it the length comes from your brief alone.
- **A Flow project can be handed to someone else.** Every export so far turns
  the artifact into something else and loses whatever that format cannot carry.
  The export menu now also offers the project itself: every file as it stands,
  in one ZIP, in any mode. It downloads rather than landing in the project, and
  it carries your work and nothing else - not the version history, not the
  backups, not the deployment settings, and none of the app's own bookkeeping.
- **Auto: the brief picks the output type.** A pitch deck for a coffee brand
  already says deck, and already says which structure, so translating that into
  a chip and a template before you have described anything is the same work
  twice. The Auto chip reads the brief and picks both, shows what it chose so
  you can correct it, and starts the run normally from there. If the helper
  model is unavailable it falls back to a keyword guess rather than to a Start
  that does nothing.
- **Ask first: a few questions before anything is built.** Flow could already
  ask mid-run; this asks while it still costs nothing, as a card of clickable
  choices, and folds your answers into the brief instead of turning them into a
  second turn. Anything you leave on "decide for me" is left out entirely, and a
  brief that is already specific gets no questions at all.
- **Undo and redo in the Flow editor.** The file editor is a plain text box, so
  the only undo it had was the browser's, which breaks the moment the text is
  set by anything but a keystroke. It has its own now, reachable from the
  toolbar and from the usual shortcuts: typing groups into words rather than
  characters, a line break always ends a step, and the cursor comes back to
  where the edit was instead of jumping to the end of the file. Taking a version
  the app put there over yours is undoable too.
- **Asking again keeps the answer you already had.** Regenerate an answer, or
  reword the question, and the previous turn is kept as a version instead of
  being deleted. Arrows and a counter at the answer step through them; switching
  restores the whole turn it belonged to, including its tool results, and
  survives a reload.
- **A deck leaves Flow as PowerPoint, a document as Word, a course as SCORM.**
  All three had exactly one way out until now: PDF. PowerPoint is built from the
  laid-out slides, so the text boxes are real ones the client can retype, with
  the pictures, the speaker notes and the slide colours; an artifact with nothing
  extractable in it exports as exact slide images instead, and says so. Word
  keeps headings, lists, tables and pictures editable. A course packages the
  project for Moodle and other LMS, with a runtime that reports the lesson
  complete inside the LMS and does nothing at all outside it, so the same file
  still previews here.
- **GLM and Qwen are providers you can pick.** DeepSeek was the only
  China-frontier provider you could just choose; these two were reachable through
  nothing but OpenRouter or a custom endpoint you had to wire yourself. Both are
  registered the whole way through: the provider card and its model list, the
  context and output limits, the live model refresh, the group-chat and
  custom-skill pickers, onboarding, and what Skales says about itself.
  Descriptions in all twelve languages.
- **Gemini 3.6 Flash and Flash Lite** are in the model lists, and are the
  default.
- **GitHub as an MCP server without Node.** It can now be added over its official
  remote endpoint with a sign-in, so it needs no npx and no access token at all.
  The local server stays, and both entries say which is which.
- **You hear about it when an agent schedules itself.** An isolated agent can
  give itself standing work that from then on wakes on its own, unattended. The
  only way to find out was to open the Schedule page. It is a notification type
  of its own now, so it reaches WhatsApp, Telegram and the phone with your quiet
  hours and channel choices applied, and it can be switched off like any other.
  You scheduling something in your own chat is not news to you, so that stays
  silent.
- **An isolated agent gets a memory of its own.** Each agent now has its own
  store, and its lessons go to its own file. Normal chat, custom agents and
  scheduled runs keep the owner's memory unchanged.
- **Look at an image by URL.** Image analysis takes an http(s) address now,
  instead of the download-then-look detour that put every picture the agent only
  wanted to see on the disk.
- **The no-time-limit setting is reachable.** A checkbox next to the slider, and
  the message at the end says what actually stopped the run.

### Added
- **Right-click a chat message for what you can do with it.** Chat bubbles had
  a small row of buttons that only showed on hover; a right-click did nothing.
  Now it opens a menu: Copy, Quote in reply (drops the text into the composer as
  a quote, ready to answer), Save to document, Read aloud, New session from
  here, Delete - plus Regenerate on an answer and Edit and resend on your own
  message. Deleting asks once before it happens, and right-clicking a passage
  you have selected leaves your normal copy alone. The menu it uses is one
  shared component, so the same behaviour is coming to the other lists.
- **Right-click a chat in your history for everything you can do with it.** The
  same menu now opens on a saved chat, in the History page and in the session
  list in the chat header. It offers Open, Pin, Rename, Archive, Download and
  Delete. The header list used to offer only a delete button on hover; now it
  has the full set, renames a chat in place, and deleting takes two clicks
  instead of a dialog. In all twelve languages.
- **Right-click a task or a schedule for its actions.** A task card now opens a
  menu with what fits its state - Run while it is waiting, Stop while it runs,
  Copy the title, Copy the result once it is done, and Delete. A schedule row
  opens Run Now, View Logs, Copy, Edit, Pause or Activate, and Delete. Both use
  the same menu as the rest of the app, delete takes two clicks instead of a
  dialog, and a system entry keeps only the actions it is allowed. In all twelve
  languages.
- **The image menu now works when you open Skales in a browser too.** Right-
  clicking a generated image, a Flow image or an image in a preview used to give
  the app's menu only in the desktop window; from the phone companion or a
  browser tab you got the browser's plain menu with no "Open in Studio". Now the
  same Save image, Copy image and Open in Studio menu appears in a browser as
  well, including for images inside a live preview. In all twelve languages.
- **Right-click an image or a link and get the menu you expect.** Images and
  links had no menu of their own anywhere in the app - not in chat, not in Flow,
  not in a preview, not in the built-in browser. Right-clicking an image now
  offers Save Image As, Copy Image and Open in Studio (which drops it straight
  into the image generator as a reference); right-clicking a link offers Copy
  Link and Open in Browser. Saving keeps the original file when it can, so a
  JPEG stays a JPEG, and falls back to a copy of what is on screen when it
  cannot, telling you by the name it suggests. It works the same in every window
  and in the in-app browser, in all twelve languages.
- **Right-click a link in a chat answer for more than copy.** A link in a
  reply now opens a menu with Copy link, Open in browser, Open in the built-in
  Browser, and Summarize - which starts a fresh chat that fetches the page and
  sums it up for you. The full menu shows in the desktop app and just as well
  when you open Skales from your phone or the web, in all twelve languages.
- **Right-click an API key field for what you want to do with it.** A key or
  secret in Settings stays a row of dots, and the only way to remove one was a
  small icon that appeared inside the field. Right-clicking a key field now
  offers Copy value - the real key goes to the clipboard while the field stays
  masked - Reveal or Hide, and Remove, which asks once before it clears. It
  works on every key in Settings, from the provider keys to the image, video and
  search service keys, in the desktop app and over the web, in all twelve
  languages.

### Fixed
- **A stuck reasoning model gets the bigger reply budget when a bigger one
  exists.** When a model spends its whole reply budget thinking and produces
  nothing, Skales retries it with more room. That retry was only offered when the
  turn had been running at the model's own limit, so a turn that had been capped
  lower - a background run, a Playground call with a set limit - was denied the
  extra room and re-ran on the same starving budget, then gave up. The retry now
  measures against what the turn actually used, so it gets the larger budget
  whenever there is one to give.
- **Things that are supposed to exist once now exist once.** Inside the app, a
  page you open and an action you trigger were each running their own copy of
  the same machinery, and the parts that only work as a single copy quietly
  came in pairs. The effects were spread across the app and all had the same
  root: the autopilot's hourly call limit could be counted twice, so it allowed
  roughly double the number of calls you set, and raising the limit in Settings
  only reached one of the two counters; switching autopilot off could leave a
  second heartbeat still ticking; a connected tool server could be started
  twice and stopping it left one running; the browser Skales controls could be
  reported as closed while it was open; the desktop could announce itself twice
  on the local network; a log file could be rotated by both copies at once and
  lose the lines the other had just written; a calendar settings change only
  took effect on one side; and two files being saved in the same instant could
  share one temporary name, which is a way to lose what was just written. All of
  it now lives in one place per app, as it was always meant to.
- **A project's deploy password stopped travelling with the project.** The
  settings a project keeps for publishing to your own server - host, user and
  password - are stored in a file inside the project folder. That file was left
  out of the project download, and nowhere else: it was listed among your
  project files where it could be opened and edited, it came back over the
  preview address to anyone who could reach your desktop, every saved version
  kept a copy of it, and it went out again inside a course package and a version
  download you hand to someone else. Publishing even uploaded it to the very
  server it describes. Now one list decides everywhere, and the files Skales
  keeps for itself - the project record, the deploy settings, the saved versions
  and the caches - stay out of every list, every download and every page you can
  open. Your work is untouched, and so are the versions you already have: they
  simply stop carrying the password with them.
- **Turning the effort dial down actually turns it down.** On a model without
  its own reasoning setting, picking Low or Medium for a chat still sent the
  full deep-reasoning instructions whenever the global toggle was on, so the
  answer was as slow and as expensive as before. The dial is the newer, more
  specific instruction and now decides: High and Extra high add the deep pass,
  Low and Medium leave it out. With the dial untouched, nothing changes.
- **A third of every request stopped being sent twice.** The instructions that
  frame a turn (the Flow brief, Code mode, a goal's plan, the live working
  state) were being placed at the top of the request AND appended at the
  bottom, so the model read the same pages twice and you paid for both. On a
  measured Flow deck turn the request fell from 86.3 KB to 56.6 KB, a third
  less, on that turn and every one after it. Nothing was removed: the second
  copy was the only thing dropped.
- **Your own instructions to Skales apply again.** The duplicate had taken the
  place where the personality and the system prompt you write in Settings
  belong, so on any turn that carried a frame, which is every Flow turn, every
  Code turn, and every step of a chat after the first, Skales ran without them.
  They are back where they belong, in front of everything else.
- **A picture sent over WhatsApp is answered, even when the picture does not
  arrive.** WhatsApp hands a photo over in two steps, and the reply was being
  written before the second one finished, so on a slower computer the file was
  never there. Sent on its own, the message vanished without a word; sent with a
  caption, only the caption came through and nothing said a picture had been
  missed. The file is now waited for and asked for again a few times, and if it
  truly does not arrive you get an answer saying so instead of silence, with the
  reason in the WhatsApp log. The same goes for anything this channel does not
  read yet, like a video: it is named rather than quietly ignored. Telegram had
  the same quiet corner and now says it too.
- **A note about an image no longer disappears behind its caption.** When the
  picture arrived but could not be read (no vision provider, a provider error,
  or image reading switched off for WhatsApp), the explanation replaced your
  message instead of joining it, so a photo with a caption looked like an
  ordinary text message and the reply never mentioned the picture. Both are kept
  now.
- **WhatsApp keeps working after an app update.** The record of which WhatsApp
  Web build your session runs against was stored next to the app itself, where
  macOS does not allow writing at all and where a Windows update deletes it. It
  now lives with your data, and an existing one is carried over, so a connection
  that works today keeps working after the next update.
- **A slide is centred on any screen, not only on a 1920x1080 one.** A deck is
  laid out at a fixed size and scaled down to whatever window it is shown in,
  but the scaled slide was being pushed towards the bottom right instead of
  sitting in the middle, so part of every slide was cut off in the preview and
  in any window smaller than full HD. It is anchored to the centre now, at any
  size. Exports were never affected and are unchanged.
- **The media settings you picked are actually used.** Aspect ratio, quality and
  a preferred model reached the model only as a sentence asking it to honour
  them, so a project set to 9:16 kept coming back square with nothing saying the
  setting had been dropped. They are now written into the generation call
  itself, and they win over what the model typed. Only values the tool can
  actually take are written: the rest is named in the result, so the reply says
  the picture is 4K only when it is. A model you name mid-project still wins over
  the preference you set on the landing.
- **A message can no longer be lost when two parts of the app write the same
  chat at once.** The lock that serializes chat writes lived per copy of a
  module, and the app loads that module more than once, so two writers could
  each hold their own lock, write the same temporary file, and leave the loser
  with nothing to rename and its messages gone. Seen live: a tool result
  vanished from a Flow turn, leaving the model to answer about work it could no
  longer see. The lock is now shared, and each write uses a temporary file of its
  own, so even a writer the lock cannot reach costs at worst an overwrite instead
  of a lost message.
- **A turn no longer loses the file you have open in the editor.** Nothing
  re-read that file after a turn wrote it, so the editor kept showing the
  version from before and the next Save quietly put it back over the new work.
  The editor now follows the file: with nothing unsaved it simply shows the new
  content, and with unsaved changes it says the file was rewritten and lets you
  keep yours or take the new one, instead of one of the two disappearing.
- **Leaving a file in the editor and coming back keeps your unsaved changes.**
  Switching to the preview and opening the file again re-read it from disk and
  threw the edits away without a word.
- **The version history is reachable while you are editing a file.** The editor
  takes the place of the preview, and with it the only way into the history.
- **Present a deck, not just show it.** A deck opens in a Presenter view of its
  own now: the current slide, the one coming next, the speaker notes for what
  you are on, an elapsed timer and the clock. The audience gets its own window
  with nothing but the deck, and the two move together, from the buttons or the
  arrow keys. Decks are also written with speaker notes from now on - what you
  say over a slide, not a repeat of what is on it - and those same notes travel
  into PowerPoint and into a new PDF handout that puts each slide next to its
  notes. A deck that has no notes says so instead of handing you empty pages.
- **Build a Brand Kit from a website.** Paste an address into the Brand Kit
  settings or into the New kit dialog in Flow, and the kit fills itself from the
  page: the palette, the heading and body typefaces, the logo, the name, the
  tagline and a short description, with the site kept as a reference link.
  Colors and fonts are read out of the page and its stylesheets, so what lands
  in the kit is what the site actually uses, not a close-looking guess - the
  page says which color is its surface and which is its body text, and the roles
  follow that rather than whichever color happens to appear most. Nothing is
  saved by reading a site: the fields are filled and you still press save, so a
  bad read costs a glance.
- **Using the logo can be decided mid-project.** Which Brand Kit a Flow project
  designs with switches from the toolbar at any time, but the "use logo and
  brand assets" half of that same choice could only be answered on the landing,
  before the project existed. A project started without the logo could never be
  given it, and one that was meant to stop demanding a logo file had to be
  started over. The toggle now sits under the kit in the toolbar menu, applies
  from the next turn, and stays where you left it when you switch kits or turn
  the kit off and on again.
- **The wireframe and phone modes are reviewed as themselves.** Both start from
  the same file as the web prototype, and were reviewing themselves against its
  checklist too. So a phone mockup was told to make sure its layout collapses to
  one column on a narrow window, which means nothing for a strip of fixed phone
  screens and invites the run to break the strip, and neither mode was ever
  asked whether it had done the one thing that mode exists for. Each has its own
  review now: the phone mode confirms it built phone screens rather than a
  website, with buttons in thumb reach and the labels the product would ship;
  the wireframe confirms it stayed grey, kept its image boxes crossed out and
  annotated, and used the real navigation. Both also say where a picture comes
  from, which neither did before: the phone mode may find and place real images
  like every other mode, and the wireframe deliberately fetches nothing and
  keeps labelled boxes, instead of each run deciding that for itself.

- **Stop now ends the session.** Stop reported success whether or not it stopped
  anything, so with a stale run the panel said stopped while the run kept
  generating, and reopening found it still live. A stop now answers honestly and
  sweeps the session: the run you are looking at first, then every other live run
  on it. Chat does the same sweep.
- **A stopped run stops looking like a failed provider.** The fallback chain read
  a stop as a dead provider: it walked the rest of the chain and put the primary
  on cooldown, so pressing Stop quietly moved the next turn to another provider
  and the run ended in "all providers failed". A stopped run now ends the chain
  and says it was stopped.
- **No time limit now means no time limit.** The task timeout has documented 0 as
  run-to-completion for a while, but the code took the zero literally: the
  deadline fired on the next tick and the task died before its first step. An
  uncapped run is now judged on whether it is still doing something, and ends
  only after fifteen quiet minutes.
- **A resumed workday remembers what it already tried.** The task loop had no
  repeat guard, so a stuck model reissued the same call with the same arguments
  until the clock ran out, and the checkpoint carried that stuck state into the
  next resume while every counter restarted at zero. The guard now rides along in
  the checkpoint, so "already tried" means across the whole chain. A chain that
  adds nothing new ends itself; one that is actually advancing resumes for as
  long as it advances.
- **A chain of runs stops saying it is done seven times.** Each round announced
  itself as finished. A run that queued its own continuation now holds its
  notification and its report until the chain really ends.
- **Continuing a task by hand keeps the agent it belongs to.** Typing "continue
  the task" carried the work on in your chat as the default agent, no matter
  which agent the task was bound to, with that agent's identity and data replaced
  by yours. It is now queued as a continuation under that agent, and the chat
  says so. An unbound task continues in chat exactly as before.
- **Saving a chat stops blocking on every message.** Each message read the whole
  session file back, stringified it and wrote it, all synchronously, on paths
  that are mostly remote-facing. Creating a session also wrote its own file
  outside the shared writer, so a new chat skipped the auto title, the base64
  stripping and the message cap.
- **Skales asks its questions again.** It could ask you a question through a form
  card since v11.3, and mostly did not: only one way of writing the call was
  accepted, so a model that phrased it slightly differently produced nothing, and
  a failed tool goes to the model, not to you. It retried until the turn ran out
  and the session ended empty. All those shapes are read now, and a question that
  still cannot be shown says so on screen instead of vanishing.
- **Test Connection tests what you typed.** It only ever tested the saved
  provider, so pasting a key and pressing Test failed while the long way round
  worked.
- **Fetch models and the key remove button exist on every provider card.** Both
  were built into a block only five providers render, so Fetch existed on
  OpenRouter and nowhere else. The remove button also moved into the key field as
  an icon, so the card stops jumping when a key is saved.
- **MCP servers can find the tools you installed.** A double-clicked app is
  started without a login shell, so nothing installed with Homebrew, nvm, fnm or
  into a local bin folder was visible, and every npx-based MCP server died with
  "spawn npx ENOENT" while the same command worked in Terminal. The usual install
  locations are now on the path a server is started with, anything you set
  yourself still wins, and a command that cannot be found is reported by name
  with the two ways out.
- **A skill loads when you name it, not when a word contains its name.** A skill
  was pulled in on a substring, so one called "teach" matched "can you teach me
  about anything at all" and pushed ten kilobytes of unrelated instructions into
  the prompt. Naming a skill still loads it, including the slash form; a name
  buried inside another word does not.
- **A stranger cannot stop your turn in a Telegram group.** Everyone writes to
  the same chat, and the stop machinery only knew the chat: someone else typing
  "stop" killed your running turn, and someone else's chatter counted toward the
  follow-up threshold. Both now follow the sender. One-to-one chats and WhatsApp
  were never affected.
- **Importing a skill brings its files.** The GitHub import asked whether a skill
  had scripts and references, recorded the answer, and then downloaded neither,
  so a skill claimed to ship with its scripts while any instruction pointing at
  its own files failed. Both folders are fetched now, and the flags describe what
  actually landed on disk.
- **Long-term memory asks where a fact came from.** Memory harvesting mined
  assistant turns without asking about origin, so a summary of a stranger's email
  or a fetched page was a candidate like any other, and a sentence planted inside
  that content could walk into memory through the summary. A turn that pulled in
  content written outside the conversation is no longer mined. Your own files,
  your own machine and your own messages are unaffected. The cost is real and
  stated: a fact you mention while discussing fetched research in that same turn
  is lost with it.
- **An error from a phone-initiated turn opens the right conversation.** It
  carried no session, so tapping the notification opened the chat landing instead
  of the conversation it belonged to.
- **A finished answer is not argued with.** Any sentence anywhere that looked
  like an announced next step re-prompted a completed answer up to three times,
  including recaps of work already done and offers handed back to you. The check
  now reads the closing stretch of the answer and no longer mistakes an offer for
  a promise.
- **The activity log stops losing lines.** Its rotation state was kept per copy of
  a module rather than shared, so two parts of the app could rotate at once and
  the loser's lines were lost.
- **A scheduled job keeps its shape and its agent.** The cron API wrote its own
  envelope into the job file, so an update persisted the word "update" as a
  field. A job can also be bound to an agent when it is created, rather than
  needing a second call, and a run started over the API is filed as the manual
  run it is.
- **Good files stop being called broken.** The download validator was rejecting
  real files: a favicon served as PNG bytes, a one-pixel GIF under the size
  floor, an SVG with a licence comment in front of it, a ZIP with a stub before
  its first entry, and a QuickTime file that opens on a different box. The
  bot-wall case it was built for is still caught.
- **Reveal in the file manager stays a reveal, not a launch.** The action that
  shows a folder or file in Finder or Explorer handed the path to the system
  opener as-is, and the opener treats some paths as "run me" rather than "show
  me": an app bundle, a script, a desktop launcher would start instead of being
  revealed. A file is now handed to the opener in reveal mode, so it is always
  shown in the file manager and its own handler is never invoked; a folder still
  opens as before. The same endpoint also turned away a request coming from
  another site in your browser - a page you happen to be visiting could reach
  the local app and ask it to open something. It now accepts the request only
  from Skales itself.
- **Uploading a skill no longer runs it.** To show a skill's name and
  description in the list, the app used to execute the uploaded file, with full
  access to the machine, the moment it arrived - before you had looked at it,
  and whether or not you ever pressed anything. A file that did its damage and
  then failed was simply filed as broken, so there was nothing to notice. Those
  few lines of information are now read out of the file as text, without running
  any of it, and every skill in an uploaded archive is treated the same way. A
  skill runs when you run it, and not before. Uploading also stopped accepting
  requests from other sites in your browser.

## v12.5.4 - Pictures and Providers

Sending a picture is a normal chat turn now. It was not: it ran on a path of its own, outside everything the app does to keep an answer alive, and the previous release patched that path instead of removing it. The path is gone, and the same class of loss is fixed everywhere else it hid - in Buddy, in the AI Pointer overlay, and in the model recognition that quietly shrank a new model's context window to an eighth of its real size. Moving that turn onto the normal path also broke a promise nobody could see from the outside: an incognito chat with a picture in it was being written to disk. An incognito chat now runs on a conversation that has no file at all. Moonshot AI (Kimi) joins as a provider.

### Fixed

- **Incognito keeps its promise on picture turns.** Since image turns became normal chat turns, the answer to a picture was written to the chat file by the part of the app that generates it - which never knew about incognito. An incognito chat with a picture in it was saved, and saved half-blind: your own message suppressed, the reply kept. An incognito chat now runs on a conversation that exists only in memory: nothing about it is written anywhere, not the messages, not the live reply, not an entry in History, and it is gone when you leave incognito or close the app.
- **The app's own nudges stop being mistaken for your instructions.** When a turn needs a correction, the app writes one into the conversation the same way you would, because that is the only way it reaches the model. Everything that then looked back for "what did the user ask?" found the correction instead: a parked task could adopt a nudge as its objective, and it could be written into memory as something you had said. A checklist created during such a turn also went invisible, so a task that had a plan stopped continuing on its own. The corrections still reach the model, unchanged; they are no longer read back as yours.
- **Stop reaches an image turn during the handoff too.** Stopping while the picture was being prepared cleared the screen, and the turn then started anyway a moment later: the answer arrived after you had stopped it. It now ends the run it just started.
- **Buddy still sees the screenshot after you approve an action.** Approving a tool continues the same turn in a second request, and that continuation went back to the stored conversation - where the picture is a placeholder by design. The stored picture is read back for the continuation, so the rest of the turn is answered about the screenshot too.
- **A picture no longer pushes the conversation out of the request.** An attached screenshot was sent at full resolution - several megabytes - and the app measured that literally when deciding how much history still fit, so one picture could take the entire budget and the model answered it with no conversation behind it at all. Pictures are now sent at the size every provider scales them to anyway (the stored copy stays full resolution), and they are counted for what they cost instead of by their raw size. Long chats with images also got noticeably faster.
- **Moonshot's region switch works everywhere it should.** Refreshing the model list ignored the picked region and asked the international host with a China key (two separate accounts - the key is invalid there), so Refresh reported no models on a correctly set up provider. The selected region also did not look selected, because it was painted with colours that only exist in the AI Pointer window. Both fixed, and a stored address in a slightly different but equivalent form is now recognised as its region.
- **Buddy sees the screenshot you attach.** Attaching a picture in Buddy showed it in Buddy's own bubble and then answered past it, because the image never reached the model. Session files deliberately do not store raw image data, and Buddy read its history back from that stored copy - so what arrived at the model was the placeholder the storage layer had left behind. Opening the chat afterwards showed that same placeholder as a broken image. The picture is now stored as a file the chat can render, and the live one is handed to Buddy directly, routed to a model that can actually see.
- **Pictures from Buddy and the AI Pointer show up in the chat.** Those turns store the picture as a file and no preview copy, and the chat bubble only ever looked for the preview - so opening the chat afterwards showed the message with no image at all. The stored picture is now shown directly.
- **A screenshot sent from the AI Pointer overlay to the chat keeps its picture.** The overlay sent the image along with the question, and the chat import ignored it, so the turn arrived as text with the picture missing.
- **New model generations work the day they land.** Model recognition was written as per-version lists, so each one broke the moment a vendor shipped the next number. A Gemini generation newer than the app ran through OpenRouter at an eighth of its context window and an eighth of its output limit, which made long chats compact themselves far too early and cut long answers short. Kimi's vision-capable generations were a literal two-entry list that the next release would not have matched. Both are rules now: a new Gemini or Kimi generation is sized and detected correctly without waiting for a Skales update.
- **Extra custom endpoints get their real context window.** Every endpoint added under Additional Custom Providers fell back to the smallest possible window rather than the one custom endpoints are meant to get, so a 128K model was run at half of it and conversations started compacting after a handful of turns. The single built-in custom slot was never affected.
- **The switches on a custom endpoint do something.** On endpoints added under Additional Custom Providers, turning tool calling off did nothing - the tools were sent anyway, and an endpoint that cannot call them answered with garbage. The Vision checkbox was worse: nothing in the app ever read it, on any custom endpoint, so images were stripped out before the request left regardless of what you ticked. Both are honoured now, and both still default to what they did before.
- **An image turn no longer reports itself dead while it is working.** The composer watches for a locked input with no live turn behind it and frees it after 25 seconds. An image turn never registered as a live turn, so on every picture you sent - since the release that added that guard - the app unlocked the composer and said the run had stopped responding, while your Vision Provider was in fact still reading. Reading one image with a local model takes longer than that on its own, several images take minutes. Image turns are real turns now, and the guard leaves them alone.
- **You can watch an image answer arrive.** The reply now streams token by token like any other, instead of showing a spinner until the whole answer is finished. On a long answer from a local model that is the difference between a working turn and a turn that looks frozen.
- **Stop stops an image turn.** The button could not reach the call that was running; it cleared the screen while the work continued in the background and the answer landed afterwards anyway. It now ends the actual turn.
- **Your custom agent answers your images.** With an agent selected, an image turn used its model but not its instructions, so the picture was answered by the default assistant wearing the agent's name. The agent's own prompt and its assigned skills now apply to an image turn. A picture handed straight to the chat model is still answered without tools, so that the model reads the image instead of reaching for something else; with a Vision Provider set up, the turn keeps the full toolset.
- **A reply cut off mid-sentence finishes itself.** When a model hit its output limit, the answer simply stopped where it stopped. The rest is now requested and joined onto what came before, so one complete message lands. This applies to every chat turn, and to the ones nobody is watching: a task, a Flow run or a scheduled run used to accept a half sentence as its finished result and close on it.
- **A picture that could not be answered says so, permanently.** If starting the answer failed outright, the error showed once and was gone: after a reload the picture sat there with no reply at all, and the model later saw a conversation ending on an unanswered question. The failure is now part of the conversation. The helpful guides for the two most common causes - the Ollama vision model is not installed, or the Vision Provider has no key - are also back, in all twelve languages; they had been lost in the rebuild and every cause showed the same generic text.
- **Telegram and WhatsApp answers carry the same protection as the desktop chat.** The way those two channels handed their instructions to the model switched off the assembly step that, among other things, tells the model to treat fetched web pages, files and messages as data rather than as instructions. Both channels now go through the same assembly as everything else, and material coming back from tools is marked as untrusted content there too.
- **A reply cut off mid-sentence finishes itself on every channel.** The repair that stitches a truncated answer back together existed only in the desktop chat. On Telegram and WhatsApp a long reply simply ended mid-sentence; from the phone it was worse - the beginning of the answer was dropped and the message started mid-sentence. Buddy never checked at all. All four now request the missing remainder and deliver one complete message.
- **A model that thinks itself empty gets rescued on every channel.** When reasoning consumes the entire reply budget, the desktop chat raises the budget, then turns reasoning off, then says honestly what happened. Telegram, WhatsApp, Buddy and the phone either retried the identical doomed request or invented a cause: Buddy said a bare "No response.", the phone blamed your provider settings. All of them now climb the same ladder and, if it truly ends empty, tell you what actually happened - in your language.
- **A tool call torn by the reply limit no longer runs broken on Telegram and WhatsApp.** A file write cut off mid-content executed anyway with half its content or failed on unreadable arguments. Like the desktop chat, both channels now hold the torn call back and ask the model to redo it in smaller pieces.
- **A picture sent from the phone reaches a model that can see.** An image turn from the phone ran on whatever model was active; if that model cannot process images they were quietly removed before the request went out, and the answer ignored the picture. The turn is now routed the same way a desktop image turn is: to a model that can actually look at it.
- **The recovery steps a stuck turn is supposed to take now actually happen.** When a model spends its whole budget on internal reasoning, repeats a failing tool call, or announces a step and then does nothing, the app sends it a correction and tries again. Those corrections were being dropped before they left the app, so the retry re-sent the identical request and the model repeated itself. They reach the model now. Instructions from your own hooks were dropped the same way and now arrive too.
- **An incognito chat stays incognito, and stops being lost.** Leaving the chat page and coming back reopened the incognito conversation as an ordinary chat: no purple marking, no "nothing is saved" notice, and because it kept running on a conversation that has no file, everything written in it from then on was saved nowhere and gone at the next start. Coming back to an off-record chat now finds it marked as one.
- **The picture in an incognito chat is not written to disk either.** Attaching an image saved the full-resolution original into the workspace uploads folder before the chat even existed, and nothing removed it afterwards, so it stayed readable by every file tool long after the conversation was gone. Off the record now covers the picture too.
- **The Buddy carries the same protection as everything else.** The way the Buddy handed its instructions to the model switched off the step that tells the model to treat fetched pages, files and messages as data rather than as instructions, and its tool results were not marked as untrusted. It is the assistant with the widest reach and the one that runs a step without asking, so it needed that protection most. It now goes through the same assembly as the desktop chat and the two messaging channels.
- **A cut-off answer no longer disappears when finishing it fails.** A reply that hit the output limit is continued and joined into one message, but if that second request failed or you pressed Stop while it was running, the half you had already watched arrive was thrown away and only an error remained. What was produced is kept now, whichever way the turn ends.
- **A picture is no longer sent to a model that cannot see it.** On providers where the app cannot swap in a seeing model by itself, an image turn from your phone or from the Buddy went out to whatever model was active, and a text-only model answered with the provider's raw rejection. The turn now says plainly that the model cannot read images, and how to set up one that can.
- **Kimi's own default model reads pictures again.** Recognition was written around version numbers, and the rolling model name Skales itself ships as the Moonshot default carries no version number, so the app treated its own default as unable to see: through OpenRouter it quietly moved the picture to a different vendor's model, and on Moonshot it reported the model as blind. That name and the 2.5 generation are recognised now.
- **Gemini's image models get their real context window.** They were sized by the family rule at a million tokens, thirty times their actual window, so the conversation was never compacted, the context meter read far too low, and the request failed once the chat passed the real limit. They now carry their own sizes, and a picture-model released later is sized cautiously instead of far too large.
- **"Test connection" honours the Moonshot region.** The button always tested against the international host, so a correctly set up China account reported an invalid key, and a passing test saved a configuration still pointing at the other service. The region you picked is now used for the test and stored with it.

### Added

- **Moonshot AI (Kimi) is a provider you can just pick.** Paste a key, choose International or China, and it works the way every other provider works: in chat, in Lio AI, in Codework, in Flow, in group chats, in custom skills, in the onboarding, and in the model picker everywhere models are chosen. The two Moonshot services are separate accounts with separate keys, so the region is a switch rather than something to type. Kimi's context window, its tool-call handling and its tuning profile were already in the app; what was missing was the provider itself.

## v12.5.3 - Flow Hotfix

The 12.5.2 reasoning watchdog cut healthy long-thinking runs: models that built a motion graphic fine one day earlier spent half an hour in cut-and-retry ladders the next. The guard is now behavioural, the stall card actually does what it offers, custom widgets are usable again in every theme, and custom agents see your connected MCP servers again.

### Fixed

- **A custom agent sees your MCP servers again.** The chat agents you build yourself run on their own prompt, which left out everything about the environment they work in: the connected MCP servers were never mentioned to them, and the tools were waiting behind a load step the agent was never told about. Asked about a connected server, such an agent answered that none were set up. Custom agents now get the same truthful MCP status as the default assistant - which servers are connected, and the exact step to pull their tools in - plus the index of every other tool group they can load.
- **An image with text gets a real answer.** Sending a picture along with a question could end in a silently empty reply: a reasoning model that spent the whole turn thinking produced no visible text, and the image turn had none of the safeguards normal chat turns have, so nothing appeared and no error was shown. In older versions the same gap showed up as a reply cut off mid-sentence. An image turn now retries once asking for the answer directly, stitches a cut-off reply back together with one continuation, and when a model truly never answers it says so honestly instead of staying silent.
- **New vision models are recognized without waiting for an update.** Skales asks your Ollama daemon what each model can actually do instead of matching names against a built-in list, so a model that just came out is treated as vision-capable the moment it appears. Kimi K2.6 and K2.7 stop being flagged as text-only, and the name list stays only as a fallback for older daemons.
- **The image-description row fits its content.** The collapsed "Vision" row under an attached image stretched across the full bubble width, which next to a narrow image looked like a broken layout. It now hugs its label and only widens when opened.
- **Custom widgets scroll again.** A widget taller than the window could not be scrolled at all: it grew past its pane while the pane itself refused to scroll, so everything below the fold was simply unreachable. Clicking or typing also made the page slide down under the pointer, because the widget's own click handling was being mistaken for new content.
- **Custom widgets follow your theme.** They were painted in fixed dark colours, so on a light theme the text was unreadable. They now inherit the theme's own colours and switch along with it, without losing what you had entered.

- **Long-thinking models can think again.** The previous release stopped a reasoning stream once it crossed a fixed share of the reply budget, which killed exactly the deep runs it was meant to protect, and then retried the doomed step with the very same budget. The stream is now stopped only when the model is provably stuck repeating its own trace, or exceeds the physical context window; whether a budget is exhausted stays the provider's call. The pointless same-budget retry is gone, and the request no longer asks for a reasoning bound the failing models were measured to ignore.
- **The stall card's buttons work.** "Keep going" was re-raised by the next status poll within a second, and Stop had to win a race against that same poll, which could take a dozen clicks. Dismissing the card now sticks for the rest of the run, and Stop takes effect immediately while the server abort settles in the background.

- **Motion scenes no longer bleed through each other.** Scene transitions layer the incoming scene over the outgoing one, and a transparent scene showed both at once (old text under new text). Scenes are opaque by default now, and Flow renders frames from inside the transitions after every motion turn and checks them - plus a code-level check for exactly this class of conflict - firing a correction automatically when something is off.
- **Your model choice in Flow is the one that runs.** If a model was set under Settings > Chat & Code > Code model, it silently overrode the model picked in Flow: the project kept showing your choice while the run used another model, and switching models to escape a stalled run changed nothing. Flow's own pick now wins in Flow.
- **Options that promised something they did not do.** The reasoning-effort dial is hidden on models that have no such control instead of being offered and ignored. Switching mode clears a pinned image or video provider that does not exist in the new mode. A pinned skill that you switch off in settings stops riding along. Pinned-skill and style-pack chips show real names instead of raw slugs.
- **A short motion brief stays short.** Asking for 3 or 5 seconds used to arrive at the model alongside two other duration ranges from the built-in guidance, and the model spent the turn reconciling them. Your duration now overrides everything else and shapes the scene plan.
- **Image and video projects see your attachments.** Those modes skipped part of the briefing: an attached reference image was never mentioned to the model, and they had neither the fact-checking rules nor the scratchpad the other modes get.
- **Flow's option rows scroll instead of wrapping.** The mode chips, the composer options and the project toolbar stay on one line and slide sideways at every window size. Dropdown menus can no longer leave the window: they clamp to the frame, flip upward when there is no room below, and the reasoning-effort row in the model menus is always visible instead of hiding behind the model list's scroll.

### Added

- **Changing the model mid-run resumes the work.** Picking a different model while a run is working closes that run, moves the project to the new model and continues the work in place, without restating the brief. This is what the stall card's "Change model" now does, and the project's model menu behaves the same way.
- **PDF export for decks and documents.** Both modes have advertised it since Flow shipped without an actual export anywhere in the app. The preview bar now has it: a deck exports one full-bleed landscape page per slide, a document exports through its own print layout.
- **A scratchpad in every Flow project.** The agent keeps a small working-memory file - plan, decisions, open items - current while it works, and a panel in the project shows it live. It carries over into new chapters, so long projects keep their thread.

## v12.5.2 - Flow and Security

Flow gets real controls for long projects and long-thinking models, a security hole on the local command interface is closed, and the app finally reports its own version correctly.

### Fixed

- **A closed door that was standing open.** The local command interface accepted requests without a valid access token, so anything on the same network could reach it. It now requires the token, the way it always should have.
- **Reasoning models that thought forever now get stopped.** Some models spend their entire reply budget on internal reasoning and never write an answer. A live watchdog now ends a run that has clearly thought past its whole budget, instead of leaving you staring at a spinner for twenty minutes.
- **Flow's Stop button actually stops.** After a page reload it used to lose track of the run and only pretend to stop while the work kept going. Stop now reaches the real run every time, and reopening a project reconnects to it.
- **A crashing preview no longer takes the whole project down.** A render error is caught in place and recovers, instead of blanking the page and orphaning the run.
- **The app reports the right version.** System properties and the About box were frozen at an old number regardless of the installed release; they now match.
- **DevKit can be enabled on an installed app.** The developer command interface only worked from a source checkout; it now reads its config from the data directory (`~/.skales-data/devkit/`), so the CLI works on a normal install. Requires the matching DevKit CLI v0.3.0.

### Added

- **New chapter in Flow.** After many rounds of changes a project can continue in a fresh, lighter session that keeps the files and a short summary but drops the long transcript, so it stays fast and costs less. Offered on its own once a project gets heavy; your brand kit, template and model choices come along.
- **Change the model and reasoning effort mid-project.** Both were only settable when starting a project; now they sit in the project's model menu and apply from the next step.
- **A way out when a model stalls.** If a model finishes twice with nothing but reasoning and no answer, the panel offers to switch to a different model instead of leaving you stuck.
- **Option chips scroll on narrow screens.** The mode and parameter rows slide sideways instead of stacking into several lines.

## v12.5.1 - Hotfix

Connected MCP servers are visible to the AI again, background tasks stop running in circles, slow models get the time they need, and the Tasks page becomes something you can actually keep tidy.

### Fixed

- **The AI can see your connected MCP servers again.** Their tools are fetched on demand to keep messages lean, but the AI was being told they were already active, so it concluded it could only see the built-in tools. It now knows how to pull in each server's tools and does so on its own in chat, Flow and scheduled runs.
- **Slow models no longer lose a whole task partway through.** A single model reply was cut off after a fixed short limit, and because that counts as an error the task failed with no resume - painful for local and reasoning models that legitimately take longer to answer. The reply now has the full task time to finish.
- **Background and scheduled tasks now see the full results of their file reads and searches.** Until now they received only a bare count or metadata, so a task that needed to look something up could repeat the same search endlessly, burning through its time limit run after run without ever finishing. The same blind spot affected agent teams and the Buddy after a tool approval. All of them now see exactly what chat sees.
- **Deleting an old reminder on the Tasks page works now.** Rows that came from the Planner were silently ignored by the delete button, so one-time reminders could never be removed from the list.
- **Flow shows what the model is actually doing.** A reasoning model can think for minutes before the first visible word, and Flow showed only a bare "deciding" spinner the whole time. The status row now shows that the model is reasoning, a live glimpse of its train of thought, and the elapsed time. The Stop button remains available throughout.
- **An answer no longer comes back empty after a long reasoning phase.** Models with extended reasoning could spend the entire reply budget on thinking and finish with no visible answer. That case is now caught and retried once with a much larger reply budget and a nudge to answer directly.
- **Flow menus stay inside the frame.** The model and template dropdowns could open partly outside the visible area near a screen edge. On narrow screens they now open as a bottom sheet that is always fully visible.

### Added

- **The Tasks page can be searched, filtered and cleaned.** A search box, status filters with counts (All, Active, Done, Failed, Stopped), newest-first ordering, and a one-click "Clear finished" that removes every completed, failed and stopped task - running and pending ones always stay.

### Changed

- **The task time limit can now be set up to 60 minutes** in Settings, for long-running local models that need room to work.

## v12.5.0 - Skills

Skales learns skills: a built-in library it can reach for on its own, style packs that give Studio a committed aesthetic, ready-made agent teams - and an agent that keeps working instead of stopping halfway.

### Added

- **A built-in skill library, ready on install.** Skales now ships with a curated set of working skills - disciplined bug diagnosis, relentless plan interviews, research with cited sources, test-first coding, handoff documents and more. Skills marked automatic are picked up by the AI on its own when the task fits; the others run when you name them. Every skill shows its author and license right on the card, the library is searchable with category filters, built-ins cannot be deleted (only switched off), and any skill you import under the same name simply takes over.
- **Skills in chat, on demand.** Mention a skill with @ or just ask for it by name - the AI loads the full instructions itself before acting.
- **Style packs for Studio.** 74 aesthetic references - each clearly labelled as inspired by a well-known product or brand look - can be pinned to a Flow project. A pack shapes typography, color and layout mood; your brief always wins on content, and no real logos or trademarks are ever reproduced.
- **Flow knows its skills.** The Flow landing gets two new controls: pin up to three skills that ride along on every step of the project, and pick a style pack next to the Brand Kit. Your choices show as chips on the first message, so a project always tells you how it was set up.
- **Ready-made agent teams.** Organization offers five presets - Research, Build, Analysis, Content and Ops - each with sensibly prompted roles. One click creates the team; everything stays editable, and building a team from scratch works as before.
- **Images carry their origin.** A picture that really came from image generation wears a small origin badge - text that merely claims a picture does not. The generation prompt now sits in a collapsible "Show prompt" section under the image instead of flooding the chat.

### Changed

- **The agent finishes what it starts.** An answer that ends mid-plan is no longer the end of the turn: with open checklist items or an announced next step, Skales continues on its own instead of waiting for you to type "continue".
- **Scheduled work resumes itself.** A scheduled task that hits its time limit while still making progress now queues its own continuation and picks up from the checkpoint - overnight jobs no longer stall until someone pushes them.
- **One schedule, once.** Creating the same schedule twice is refused with a clear message instead of silently doubling future runs.
- **Downloads tell the truth.** A downloaded file is checked against what it claims to be - an error page pretending to be an image fails loudly instead of leaving a broken file behind, and re-fetch attempts identify the app honestly.
- **Websites that block the built-in browser now say so.** Instead of failing quietly, the run explains the block and points to the visible-browser login handoff.
- **Knowledge-graph import shows real progress** instead of sitting at zero for minutes.
- **Every provider key can be removed** - including the ones that previously had no remove button.
- **Long-term memory learns only from real conversations.** Automated feeds and error messages no longer qualify as facts about you.
- **Quitting means quitting.** The power buttons close the app and its background helpers for real instead of reloading the window.
- **A cancelled goal says so once.** The cancel notice appears exactly one time and no longer returns on every reload.

### Fixed

- Push notifications tapped on the phone open the exact conversation they belong to, even when the app was fully closed.
- Attached documents with embedded pictures no longer flood the conversation with image requests.
- Image messages no longer show stray formatting fragments beneath the picture.
- The Flow file panel closes properly, and long file writes no longer end with the whole page pasted into the chat.

## v12.4.5 - Checkpoint

The release that locks the doors before adding more rooms: remote access becomes one honest switch, pairing asks you first, videos work from the first message, and your phone can hand the desktop videos, PDFs and its whole photo batch.

### Added

- **Remote access is one switch, default off.** Off means the server listens only on this machine - in a hotel or cafe network the port simply is not there. On means reachable over LAN/Tailscale AND token-protected, as one inseparable setting; the in-between that used to be the default (reachable, no token) no longer exists. The access token now survives restarts, and Settings > Security shows it at the very top with a copy button, a QR code and a regenerate button. Swarm users are unaffected: an enabled Swarm keeps the port open for its peers.
- **Pairing asks you first.** A phone (or any device) that knocks with your pairing code no longer connects by itself: the Mobile page shows who is asking - device name plus the tail of its key - and you allow or refuse. Your Allow counts only for the device you were shown, refusing is "not now" rather than a ban, and unanswered requests expire on their own.
- **Pair without a camera.** The desktop now shows a copyable pairing string under its QR code - for remote onboarding, headless devices, or whenever scanning is impractical.
- **Video from the very first message.** The chat landing page accepts videos (picker and drag & drop) up to 100 MB, shows a thumbnail chip, and hands the analysis to the same frame-sampling flow the in-session composer uses.
- **Videos, PDFs and full photo batches from your phone.** The bridge speaks a new protocol: the paired app (2.4.5) streams videos and PDFs to the desktop in chunks, every attached image arrives instead of only the first, and the phone can display the desktop's knowledge graph. Older app versions keep working exactly as before.
- **Attach audio, and videos get their soundtrack read.** Drop an audio file into either composer and it is transcribed through your configured speech-to-text provider, then reads like any attached document - with an honest message if no provider is set up. Video analysis now also listens: the soundtrack is transcribed alongside the sampled frames, so "what is said in this clip" finally has an answer. Both are best-effort and never block the run.

### Changed

- **Server actions joined the token gate.** With remote access on, the token now guards the entire local surface - not only the /api routes but the internal action calls pages are built on. Verified against the packaged server: without a token, nothing executes and nothing is written.
- **Frames fold into an accordion.** A video message shows its sampled frames as a compact expandable strip under a thumbnail instead of ten full-size images, and the bubble reads like your question again instead of an attachment note.
- **The knowledge graph stays yours.** Machine-written sessions (briefings) and Skales' own product terms no longer enter the graph - the extractor filters them at every entry point, and Settings offers a one-time cleanup that removes previously collected ones without touching your real entities.

### Fixed

- **Friend Mode no longer double-texts.** Two overlapping schedulers could each generate their own check-in inside one cooldown window - two different messages in the same minute. One in-flight guard now spans both, including across the app's two server bundles.
- **The @ and / menus close when you click away** - and a slow, deliberate click on an entry no longer loses the selection.
- **Stop works while frames are being sampled.** The red stop button now aborts a video analysis during the "sampling video frames" phase instead of only after it.
- **The pairing QR told phones the wrong desktop version** - hardcoded at 10.1.0 since that release; it now always carries the real version.

## v12.4.0 - Piranha

Generated media shows up where you asked for it, your phone sees what the desktop is doing and can stop it, goals know when they are done, and every agent gets its own name, hooks and task list.

### Added

- **Your phone sees a running desktop chat - and can stop it.** When the paired app has a session open that Skales is working on, the phone shows a live banner with a Stop button, streams each tool step as it happens, and receives generated images right in the bubble. The desktop also gained a "send to phone" action that forwards a workspace file or note to the paired app, with a push if the app is closed. Work done from the phone now shows up as proper tool cards when you open that session on the PC.
- **User hooks.** Run your own text snippet or shell command at chat lifecycle points: session start, after a tool result (optionally one specific tool), or when a goal finishes. Command hooks go through the exact same safety gates as the model's own commands, and isolated agents never fire them.
- **Tick the task list yourself.** The checklist Skales keeps in a normal chat is now clickable when the chat is idle: mark items done or undone by hand, and your ticks survive reload. A fresh plan from the model still wins over stale hand edits.
- **Publish over SFTP.** FTP publishing now speaks SFTP as well as FTP and FTPS: pick the protocol per profile, and on the first successful test Skales shows the server's SSH host-key fingerprint and pins it to the profile. If that key ever changes, the next upload is refused, so a swapped-out server can't quietly receive your files.
- **Goals can declare themselves done.** A long-running goal with broad criteria used to be nudged to keep going forever; the agent can now state completion explicitly once the work is verifiably done, and the checker respects it.
- **Isolated agents got a real household.** An isolated agent's generated and edited images stay in its own workspace instead of your gallery, downloads are confined to its folder with a hard size cap enforced mid-download, its dates are labelled UTC instead of your timezone, planner tasks run as the agent that owns them, and each agent can have its own email account without seeing yours.

### Changed

- **Multi-agent runs report in the conversation.** The per-agent progress trace now lives under the message that dispatched the job, and the finished report keeps a compact version of it, instead of a banner over the whole chat.
- **The Knowledge Graph got a proper physics engine.** The map lays out with d3-force, settles quickly at up to 300 nodes (up from 150), and stays smooth to drag, zoom and expand.
- **Media cards are forgery-proof.** Image and video cards in chat render only from real tool results, never from text that merely looks like one, so a model cannot fake a "generated file" card. Video bubbles from before this release show their file path as plain text instead of a card - the file itself is untouched.
- **Images open in the lightbox.** Clicking an image card in chat opens the full-size viewer; the dead "Open in Studio" link on media cards is gone.
- **One-tap deploy knows Cloudflare.** Deploying a Code-mode project now recognises a Cloudflare Wrangler setup alongside Firebase, Vercel, Netlify and an npm deploy script, and points you at your own CLI for anything else.
- **Skales IQ asks for an update when it needs one.** If a build is too old for the Skales IQ service, chat now shows a clear "install the latest update" message instead of a raw error.
- **Custom agents answer as themselves.** Ask a custom agent who it is and it answers under its own name (still honest about the model it runs on) instead of introducing itself as Skales.
- **WhatsApp knows who "me" is.** "Send it to me on WhatsApp" reaches the owner instead of the bot's own number, contacts saved in national format (0676...) match, and a failed media send explains the file path problem before anything leaves the machine. The isolated-agent chat badge became a lock icon.

### Fixed

- **Generated images render in chat and Flow again.** An image produced by the built-in generator shows up as a picture in the conversation and lands in the Flow project folder, on every backend - previously the turn ended early with a broken link.
- **Video generation works in the installed app on every platform.** The Windows build was missing the Google video engine and ffmpeg entirely, and every platform's package now carries the ffmpeg binary for its own operating system instead of the build machine's. Veo model names are consistent everywhere, so the tier you pick is the tier that runs.
- **Finished answers stop re-typing themselves.** At the end of a streamed reply the text no longer wipes and types out a second time.
- **Google Calendar tells you what is actually wrong.** The dashboard widget and calendar tools now name the real cause when the connection fails - an expired authorization (with the 7-day testing-mode hint), missing OAuth fields, or a network error - instead of a generic "reconfigure" line. And an empty week on a connected calendar links to the planner instead of suggesting you set up a calendar you already set up.
- **Web search fails forward.** A failed search names the fallback that will work (fetch the page, extract text), Windows gets shell hints that match its own tools, and the keyless DuckDuckGo path retries with a hard 25-second budget instead of hanging.
- **Three tools were invisible to the model.** A manifest generator gap hid connector building, connector requests and web search from the tool catalog in some setups; all 182 tools are listed again and a guard keeps future descriptions from silently dropping out.
- **A broken video clip cannot stall the chat.** Analyzing an uploaded video now stops sampling after a minute and answers from the frames it already has, instead of hanging on a slow or partly corrupt file.
- **The chat input can no longer stay locked.** In rare cases the composer stayed disabled after a reply finished (Enter and Send dead until a hard reload); a watchdog now releases it whenever no run is actually in flight.
- **A dispatched multi-agent job is always visible.** When the dispatching reply carried no visible tool card, the live worker chips had nowhere to attach and the job looked like nothing had started; they now attach to the latest reply instead.
- **Video renders stop making promises.** After starting a background video render the model no longer invents wait times or tries to poll a progress URL; it states that the render is running and where the finished file will appear.
- **Chat exports skip empty blocks.** A reply that only ran tools exported as an empty "Skales" section; exports now show a compact tools line instead, in both the /export command and the History download.
- **Background runs stop paying for the whole tool catalog.** Multi-agent workers, planner tasks and scheduled catch-up runs used to receive every tool definition (tens of thousands of tokens) on every single step; they now start from the same lean core set as chat and pull extra tool groups on demand. On a metered model this was the single largest hidden cost per day. GLM 5.x models get on-demand tool loading too, instead of always paying for the full catalog.

## v12.3.5 - Flying Gecko

Your knowledge graph comes alive, multi-agent jobs show every worker and finish on their own, and finished work stops being sent twice.

### Added

- **A living map of what Skales knows.** The Knowledge Graph on the Memory page is now a real, explorable network: it settles into shape on its own, you can zoom, pan and drag single nodes, and busier ideas sit larger with a soft glow. Labels fade in as you zoom so a dense graph stays readable, and an Expand button opens the whole thing full-screen. Light and dark both fit, and Reduce Motion drops straight to the settled layout.
- **Watch every agent in a team job.** When Skales fans a task out to several workers, the chat now shows one live chip per worker moving from queued to working to done or failed, with a running count and elapsed time, instead of a single "running" line. Click it to open the Tasks view.
- **A private scratchpad for each conversation.** Skales can now keep its own working notes for a chat and gets a private work folder per session, so intermediate files from one conversation never collide with another. Its notes come back to it at the start of every turn, so a long task keeps its train of thought.
- **Drop a video into chat and ask about it.** You can now attach a video (or drag one in) and have Skales actually watch it: it samples frames across the clip and reads them together, so you can ask what happens, check why a short is working, or figure out how a clip was made, its hook, pacing, cuts and on-screen text. Works with any capable model; camera clips just need FFmpeg, which installs from Settings.
- **Look through a camera.** Skales can now take a single frame from a camera on demand and tell you what it sees: an IP or WiFi camera address, or a camera already set up in Home Assistant (which is how a Ring doorbell is reached). Ask "is anyone at the door" or "what's in the garden", and pair it with a schedule to check a camera every few minutes and only ping you when something changes. It asks before looking, and needs a Vision Provider.
- **Isolated agents with a life of their own.** A custom agent can now be marked Isolated: it runs with its own memory, its own workspace folder and only the tools you hand it, and it never sees your identity, facts or saved memory - not even in the background passes that build your knowledge over time. Pair it with a pinned model, a bound FTP profile and a scheduled goal and it can run a project of its own, end to end. If its definition ever can't be loaded, the run stops rather than falling back to your data.
- **Publish a folder over FTP.** A new publish tool uploads a workspace folder through a saved FTP profile, and profiles can be bound to a single agent so only that agent can use them. FTPS (explicit TLS) is supported and switches on automatically when the server requires it, so hosts like Hetzner work out of the box.
- **Some Discover posts invite your agent along.** A post can now carry a "Let your agent explore this space" button: one tap prepares a visit for your own agent - it reads the site's welcome file, looks around and leaves a short guestbook comment, with your usual confirmations. You review the request before it's sent, and each post can be run once.

### Changed

- **Notifications carry their real button.** Announcements now show exactly the action label they were written with instead of a generic "Learn More", update notices get their own look with a one-tap jump to the in-app update page, and messages can be aimed at desktop or mobile users specifically.
- **Signing in by hand works everywhere.** The visible sign-in window for website logins no longer announces itself as an automated browser, so Google and other providers accept the login instead of blocking it as insecure.
- **Toast messages stay put.** Stacked notifications in the corner could leave an invisible gap behind that pushed each new toast lower until the stack drifted toward the middle of the screen. Old toasts are now always cleared, and any that a background window left stranded are swept up on the next one, so the stack stays anchored top-right.

### Fixed

- **A team job finishes on its own.** When Skales split a goal across several workers, it could stall once they finished and wait for you to nudge it. It now picks the goal back up automatically the moment the workers are done and carries it to the finish, whether or not the chat is open.
- **Finished work is never sent twice.** Re-checking a completed goal could re-run steps that had already happened, so a file or message could go out two or three times. Once a step has genuinely succeeded, its result now counts as proof and is never repeated to "re-verify" it.
- **Ask for an image, get an image.** When you ask for a picture, poster or graphic, Skales now creates it instead of handing back a text description of what it would draw. If no image provider is set up, it says so plainly and points you to Settings rather than quietly returning a prompt. And right after a big task wraps up, a follow-up like "now write the launch post" reuses what was just made instead of starting the search over.

## v12.3.0 - Flying Gecko

Flow checks its own work before handing it to you, long conversations keep their task, the calendar tells you what is actually wrong, and Skales stops getting in its own way mid-work.

### Added

- **Sign in to a website, once.** When a task needs you logged in, like posting on X or checking an order, Skales opens a visible browser window so you can sign in by hand. Your login is saved to the Skales browser profile and reused the next time, so it can work inside your accounts without ever seeing your password. Open a login window any time from Settings, Browser Control ("Log in to a website").
- **Watch the browser work.** A new "Show browser window" switch under Settings, Browser Control opens a real, visible browser window instead of running hidden, so you can see each step as it happens.
- **Multi-line scripts run without shell traps.** Skales can now write a script to a file and run it directly with the right interpreter (Python, Node, Bash, PowerShell) instead of squeezing code through a shell one-liner. On Windows especially, quotes, braces and special characters used to break perfectly good code before it even ran; that whole class of failure is gone.
- **Every file change has an undo point.** Before Skales overwrites, edits, appends to or deletes a file, it quietly keeps a copy of the previous version. If an edit turns out wrong, the earlier version is right there to restore, so a working script can no longer be lost to one bad change.
- **Self-built tools carry a freshness mark.** Each tool Skales built for itself now remembers when it last ran successfully. A tool that has not proven itself recently is marked as such, so Skales tests it before relying on it and reads it before changing it.
- **Flow checks its own work before handing it over.** After Flow builds or edits a deck, prototype, page, or visual, it now takes a picture of the finished result and looks at it the way you would, checking for text that runs off the edge, unreadable contrast, pieces cut off outside the frame, or a layout that has collapsed. If it spots a clear rendering problem it quietly fixes that one thing and shows you the corrected version. It makes at most one extra pass, never holds up a finished draft, and only steps in when a Vision Provider is set up. You can switch it off under Settings, Vision Provider.
- **Fill your memory graph from what Skales already knows.** Once you turn on learning in Settings, Memory, the Knowledge Graph can now be seeded in one pass from your saved long-term facts and a slice of your recent chats, instead of only filling up reply by reply going forward. Choose how much history to pull in, watch it count through in the background, and stop it whenever you like. Running it again never creates duplicates.
- **Your desktop makes Studio visuals for a connected phone.** When your phone is paired in remote mode but has no provider key of its own, it can now ask your desktop to build a Studio visual for it. The desktop generates it on the side and sends back the finished design, without ever adding anything to your chat history.
- **Friend Mode can reach your phone.** Turn on "Mobile App" in the Friend Mode channels and your desktop's proactive check-ins are sent to your paired phone too, where they land in your Buddy thread. It uses the same cooldowns and quiet hours as every other channel, and the message stays encrypted end to end.

### Changed

- **Browsing is faster, cheaper and more reliable.** Skales now reads and acts on web pages far more efficiently: clicks and typing land precisely, long browsing tasks cost noticeably less, and even smaller local models can drive the browser now.
- **The Proactive operator says what it is waiting for.** An empty operator pane on the Memory page used to show only "No initiatives yet", which read like a broken feature. It now explains what Skales is watching (upcoming meetings, failed scheduled tasks, blocked work, unread email) and shows when the last background check ran, so quiet genuinely reads as good news.
- **Skales reads before it edits.** Changing a file it has not actually looked at in the current session is now refused once, with the instruction to read it first. What is on disk can differ from what the model remembers, and blind patches on working scripts were the main way tools got destroyed.
- **Failing approaches now end in an honest question, not a seventh attempt.** When the same tool keeps failing, Skales first gets told exactly what failed and is pushed to step back and test one hypothesis at a time; if the failures continue, it stops trying on its own, summarizes what it attempted, names its best guess at the cause, and asks you for the one thing it needs. No more endless rabbit holes.
- **Unrestricted mode keeps its word.** A few command rules used to stay blocked even with Safety Mode set to Unrestricted. In true Unrestricted they now run instead of being refused, and the result card in the chat carries a clearly visible amber warning strip naming what was flagged, pinned to the exact command it belongs to and kept in the conversation history. Safe and Advanced keep their protections unchanged.
- **The working glow is easier to notice.** The ambient background that breathes while Skales works now drifts further, on a slightly quicker rhythm, and sits a touch more present at its peak, so you can actually see that something is happening. It stays well within readable contrast, idle stays still, and Reduce Motion still switches it off entirely.

### Fixed

- **Long conversations no longer lose track of the task.** In a long session, especially one with many working steps, Skales quietly fed the model only the most recent stretch of the conversation, so the original request could fall out of view while the context meter still showed plenty of room, and a later remark could be mistaken for the task itself. The model now always works from the full conversation, sensibly condensed, and the original request stays anchored no matter how long the session gets.
- **Work already done is not redone.** When a long task was interrupted and you asked Skales to continue, it could lose track of the files it had already written, claim a script was gone, and write it again from scratch. Skales now keeps a live list of everything the task has written, checks that each file is really on disk, and picks up from there instead of starting over.
- **Skales changes course instead of giving up.** When a step kept failing the same way, Skales used to stop with a generic "I got stuck" message, throwing away an otherwise healthy task. It now tells the model exactly which call keeps failing and why, blocks that one call, and pushes it to try a different approach; only if it still cannot move on does it stop, and the stop message now names the step that jammed instead of leaving you guessing.
- **The calendar tells you why it is empty.** If none of your configured Google calendars could be read, most often because a calendar's display name was entered where its ID belongs, Skales showed an empty calendar as if you simply had no events. It now says which entries failed and how to fix them, and the settings field explains where to copy the real Calendar ID from. One broken entry among working ones still does not disturb the rest.
- **The Memory page tells the truth about Knowledge Graph learning.** The graph card claimed new facts were extracted automatically after every reply, while that learning is actually a switch that ships off, hidden under a name nobody connected to the personal graph. The card now says plainly whether learning is on, and the switch that controls it lives right there with an honest name.
- **Skales knows which Safety Mode it is running in.** Asking which mode is active used to get you a guess or a request to check Settings yourself. Skales is now told its active mode and what that mode means, and answers directly.
- **Legitimate commands are no longer refused as dangerous.** A handful of everyday commands, such as scheduling a task on Windows or listing output with a format flag, were wrongly treated as system-destroying and refused regardless of your Safety Mode. Command safety is now decided in one place, honours your chosen mode everywhere, and only the genuinely destructive commands stay blocked.
- **Windows: scripts no longer crash on special characters.** A Python script that printed an emoji or other non-Latin text could die with an encoding error on Windows. Script output now uses UTF-8 there by default.

## v12.2.5 - Past Freeze

Your phone can now start, watch and stop the work your desktop does. Agents know who you are and what day it is. Vision lands on the right provider, the composer never stays stuck, and Flow stays out of your chats.

### Added

- **The chat background breathes while Skales works.** The accent glow slowly grows and drifts while your question is being thought through, stays a touch more present while the answer arrives, and settles back when it is done instead of snapping off. A running goal gets its own livelier, greener state, so you can tell at a glance that something is still working for you. The thinking dots and the status bar move on the same tempo. Nothing moves while Skales is idle, the animation stops while the window is in the background, and it is off entirely when your system asks for reduced motion.
- **Skales Mobile is on the App Store.** The Skales Mobile page now links the App Store next to Google Play, and the iOS button opens the store in your browser like the Android one. The in-app guide and roadmap note both stores are live.
- **Start a goal from your phone.** A request the phone marks as a goal now runs on the desktop as a proper background goal, on its own session with its own plan, instead of one long blocking reply. It keeps working after the phone acknowledges it, so you can close the app and come back to the result. Goals honour the Settings goal switch and need a real provider (your own key or a local model), like the scheduled goals.
- **Control a goal from your phone.** The Skales mobile app can now list the goals running on your desktop and stop, pause, or continue any of them. Stop and pause halt the live work at once, the same as the desktop.
- **Get notified on your phone when a goal finishes.** When a goal started from your phone completes, pauses for input, or stops on an error, the desktop notifies that phone, even if the app is closed. The notification is routed only to the phone that started the goal, and it arrives as its own message so it never interrupts whatever else you are doing in chat.
- **Your phone sees the live plan.** When the desktop works through a multi-step task from a phone request, it sends the same step-by-step checklist to the Skales mobile app, which shows it as a strip above the chat, updates it the moment a step changes, and brings it back when you reopen the conversation.
- **Goal and origin badges reach your phone.** The desktop now shares each session's goal progress and whether it started on a phone, so the phone shows the same goal pill and Mobile badge you see on the desktop.
- **Browse and fetch desktop files from your phone.** The phone's Workspace shows a Desktop folder while connected: browse your desktop workspace and pull any file (up to 2.5 MB) to the phone over the encrypted link. Read-only and strictly limited to the workspace folder.
- **Finish a connector's setup from your phone.** The Skales mobile app can now list your desktop's API connectors and complete the parts that used to require the desktop: confirm the connector's domain and set its key (and the client id and token address for OAuth2). The key is sent once, stored encrypted, and never read back; confirming the domain and setting the key stay separate steps.
- **Discover shows when someone forks a skill or template.** Forking a skill or a template now posts a short note to the feed, the same way sharing does, and only when feed sharing is turned on.
- **Safety Mode now has three levels: Safe, Advanced and Unrestricted.** Safe blocks dangerous shell commands and asks before critical actions. Advanced runs without asking and allows dangerous commands, while a residual guard still refuses the handful that would destroy the machine or its data outright. Unrestricted removes those last blocks too and, with file access also set to unrestricted, lets Skales reach system locations, install software and talk to hardware. If you were on the old Unrestricted, it is now called Advanced with identical behaviour, and a one-time note on first start points you to the new, genuinely unrestricted mode. You change it under Settings, Security, or during onboarding.
- **Name an image or video model in plain words.** You can ask for a model by loose name, for example "flux schnell", "gemini flash" or "veo 3.1 flash", and Skales matches it to a model your providers can actually run, picking the right backend for it. If the name is ambiguous it asks which one you meant instead of quietly falling back to a default.
- **Skales can set up an API connector from documentation, mid-conversation.** In Advanced or Unrestricted mode, after reading a service's API docs Skales can scaffold one of your REST connectors during the chat. The connector is saved switched off and without a key; you confirm the address and enter the key yourself under Settings, API Connector before it can be used, so the key never passes through the model.
- **API connectors speak OAuth2 (client credentials).** A cloud API that exchanges a client id and secret for an access token at a token endpoint, like Husqvarna or Gardena smart systems, now works as a connector: pick "OAuth2 (client credentials)" as the auth scheme, enter the token URL and client id, and put the client secret in the key field. Skales fetches and refreshes the token on its own, entirely on your machine. Static extra headers such as an application key are supported too.
- **Tools Skales builds for itself survive the session.** When Skales writes itself a working script, say a Bluetooth scanner or a TV caster, it keeps it. Every future conversation knows about those tools, so Skales reuses what it already built instead of starting over.

### Fixed

- **Agents now know your saved facts and today's date.** A custom agent, or an agent from the Agents page such as the Research Analyst, ran with its persona only: none of your Knowledge & Facts applied, and it did not know what day it is, so a web research report could carry the publication date of its sources as the report date. Every agent now gets the real current date, is told that a date found in a source belongs to that source and is never today, and receives the facts you saved. Agents that read the web are also held to the same rules about untrusted content as the rest of Skales.
- **Your saved facts survive minimal compression.** With compression set to minimal, or chosen automatically for a small local model, what Skales knows about you was shortened so aggressively that every entry under Memory, Knowledge & Facts fell away, so a fact you explicitly saved was simply absent when the model answered. Your saved facts are now always kept.
- **Stopping a running goal from the header now actually stops it.** The Stop control on the goal activity pill only marked the goal stopped without halting the work, so it kept running and reappeared on the next refresh. Stop and Pause now halt the live run at once. A stopped or crashed goal also no longer silently resumes itself on your next message: an interrupted goal is parked so it only continues when you choose Continue, and a goal that hit an error is marked failed and leaves the active list instead of looking like it is still running.
- **A long task no longer times out on your phone while the desktop is still working on it.** In remote mode a request that ran long (a goal, a slow build, one long model step) was given up on and its real answer dropped, and a retry re-ran the whole thing. The desktop now keeps the phone informed while it works, so the phone waits and the answer arrives.
- **OpenRouter works as your Vision Provider.** Switching the Vision Provider to OpenRouter kept sending image reads to whatever address a previous provider had left behind (a local Ollama, for example), so every read failed with a "not found" error even with a valid image model, while the exact same endpoint entered as a custom provider worked. OpenRouter now always uses its own address, a leftover address from a previous provider is cleared the moment you switch providers, and a "not found" error now names the model and how to correct its id.
- **The image description sits under the picture, inside your message.** The collapsed eye view with the Vision Provider's description floated above the message bubble instead of belonging to it. It now sits inside the bubble directly under the attached image, styled to read clearly on the bubble's background, both live and after reloading the conversation.
- **The composer no longer stays stuck after a reply stops responding.** If a reply stalled partway (a hung tool call, an orphaned run, a dropped connection), the input stayed locked with no way to send until you reloaded the page. It now releases on its own after a run goes quiet for too long, with a short note that you can send again, and without stopping whatever the server is still doing. Typing in very long conversations is lighter too.
- **Skales Visuals no longer times out while it is still working.** Creating an image or visual in Flow could fail with "the operation was aborted due to timeout" even while the model was actively writing, because a full single-page visual routinely takes a while. Flow now allows the whole generation and only gives up on real silence, and when a timeout does happen the message explains what stopped and suggests trying again or picking a faster model.
- **Forked and created templates appear in your library.** Forking a template from Discover, or creating one, showed a success message but the template never appeared under Templates, because the library never read your saved custom templates. They now show up with a Custom or Forked chip and a delete button, so a mistaken fork is not permanent, and a forked template carries its full prompt instead of a short preview.
- **Flow projects stay out of your chats.** A Flow (Studio) project could surface as a recent chat on the dashboard, in history, in the command palette and on your mobile device, and finishing a Flow turn popped a "finished your chat reply" note that opened the Flow conversation inside the chat view. Flow projects are now kept out of every chat list, a finished Flow turn shows a Flow note that reopens the workspace, and opening a Flow project link sends you to Flow instead of rendering it as a chat.
- **Long answers from Ollama Cloud models no longer cut off mid-sentence.** A strong model served through Ollama Cloud was limited to a small answer length regardless of what it could actually write, so longer replies stopped partway. Each family now uses its real answer length, and you can still raise it under Override Model Limits.
- **Ollama Cloud models use their tools instead of just talking about them.** A frontier model running on Ollama Cloud was treated as if it were small hardware on your own desk, so it described what it would do instead of doing it, and a slow first response could wrongly mark it as not supporting tools at all. These hosted models are now recognised for what they are.
- **Reasoning no longer leaks into the chat as "[thinking]" text.** Some models write their private reasoning inline using square brackets, which showed up as literal "[thinking]" text in the reply. That reasoning now goes into the collapsed Reasoning section like every other model, and the visible answer stays clean.
- **Image generation works when your only provider is fal.ai or Replicate.** Asking for an image in a chat or a Flow could return a written description instead of a picture when the only image key you had configured was fal.ai, or a Replicate key stored the newer way, because those two were overlooked when deciding whether to offer image generation. Every image provider you have configured is now recognised, so the tool is offered whenever any of them can produce an image.
- **A PowerShell display command is no longer mistaken for disk formatting.** Formatting command output for display (for example listing Bluetooth devices with Format-Table, Format-List or Format-Wide) was refused as if it were a drive-format command. Only real drive formatting stays blocked now; the display formatters run.
- **Pairing a phone opens the right panel and confirms right away.** After scanning the pairing QR code, the panel jumped to Settings instead of Connected Devices, and a scan showed no confirmation until a background refresh caught up. It now opens Connected Devices immediately with a success note.
- **Removing a paired phone really cuts it off.** Removing a device only closed the current connection, so the phone could quietly reconnect on its own. Removing a phone now tells it to forget this computer and refuses to re-pair it until you deliberately show a fresh QR code, so a removed device stays removed.

### Changed

- **The interactive Playground is taking a break.** The mini-app builder is temporarily unavailable while it is reworked and will return in a later release. For now its sidebar entry, its Settings tab and its Add-Ons card are hidden, including for anyone who had it switched on.
- **Skales IQ is described as free included usage, not a trial.** The Skales IQ copy now makes clear it is free included usage rather than a trial of a paid plan, with nothing to buy: when the included usage runs out you connect your own provider and everything keeps working. Added an explicit "this is not a subscription" line to the Skales IQ settings box.
- **Flow places your attached images instead of redrawing them.** In a design or build project, an attached image is now described so the model knows what each file shows and embeds the actual file where it fits, instead of trying to recreate the picture in code. In an image or video project the attachment still passes straight to the generator untouched, so an edit works on your real picture.

## v12.2.0 - Freeze

The first release after the freeze: images answer with the model you chose, the ChatGPT subscription works everywhere, and profiles keep their full guidance.

### Fixed

- **Synced model profiles keep their full guidance.** An imported tuning profile's guidance text was cut at 600 characters on import, which silently truncated every profile in the shared library mid-sentence and dropped the tail of the instructions. The bound is now generous enough for a real multi-rule hint, and a profile that still exceeds it is logged instead of being trimmed in silence.
- **Images keep your chosen chat model.** When a dedicated Vision Provider read an attached image, the answer was silently handed to a different model than the one you picked (a local vision model, or a small cloud fallback), so the reply looked like it came from the wrong place and, on paid providers, billed the wrong model. The picture now goes to the vision model only when a raw image is actually sent; once the Vision Provider has described it, your chosen chat model answers. Vision-capability detection now reads what the local model actually reports instead of guessing from its name, a Vision URL that already points at the chat endpoint is no longer mangled into a broken address, an explicitly chosen local vision model is attempted instead of pre-blocked, and the image-related tips and error messages now point at the Vision Provider settings. Profiles can also carry a vision-capability override so a new model is recognised without an app update. A picture and its description are now saved with the conversation, so the very next question ("what did you see?") is answered from what was actually read instead of "I do not remember", and a failed image read no longer vanishes from the history. The saved description shows under the image as a small collapsed eye accordion (like the reasoning view), never as raw text in the bubble.
- **The ChatGPT (Codex) subscription is selectable everywhere.** A signed-in ChatGPT account never appeared in the model pickers (new-chat landing, in-session switcher, picker search, agent and Codework provider dropdowns) because those lists only recognised API-key credentials; the subscription sign-in now counts as configured and the Codex models show up like any other provider's. Flow already offered it.
- **Every surface reads image capability from one place.** Telegram kept its own list of which models can see, with the multimodal gemma3 and gemma4 families wrongly marked text-only, and several surfaces disagreed about what counts as a local vision endpoint. Chat, Telegram, WhatsApp and the desktop buddy now decide this the same way, a pasted address is tidied up first, and the buddy no longer insists on a vision model id where every other surface accepts the built-in default.
- **The ChatGPT (Codex) subscription is on one tested path.** The plain-chat and tool-using code for a signed-in ChatGPT account had drifted into two hand-copied copies, which is how the subscription kept breaking one release at a time; both now share a single request builder and stream parser covered by tests, so a fix can no longer land in one and miss the other. The current model list gained GPT-5.5 (an up-to-date id was falling through to the manual entry field), and a short-lived access token is now refreshed automatically before a turn instead of leaving a still "signed in" account failing every message until a manual sign-out and sign-in.

## v12.1.1 - Freeze

Long-running tools, portable backups, and Flow polish.

### Added

- **Send several images in one message.** Pasting, dragging or picking multiple pictures used to keep only the first one; now up to five images ride along per message, on the new-chat landing as well as in a running session. Each queued image shows its own preview with its own remove button, a shared window capture joins the same queue, the model receives all pictures in one turn (a dedicated Vision Provider reads each one and hands over numbered descriptions), all of them are saved with the conversation and shown in the bubble, and editing a message that carried several pictures resends all of them.
- **Discover marks mobile posts.** A post made from the Skales mobile app shows a small phone icon next to its timestamp, matching the marker in the mobile feed. Under the hood the device bridge gained session control and a tool catalog for the next mobile release; everything is additive, a current mobile app is unaffected.
- **Wrapped share cards get more looks.** The weekly recap card now offers nine themes (Skales, Obsidian, Snowfield, Neon, Sunset, Rose, Aurora, Grape and Teal), each in a light and a dark variant, chosen from a swatch picker right above the card. The choice is remembered for next week, and the download, copy and share-to-Discover buttons all export the themed card.

### Changed

- **Flow designs with a stance now.** Every authored mode carries a design essence on top of its structural seed: commit to one aesthetic direction instead of a safe generic middle, name the system (layout family, fonts, background rhythm) before building, let typography carry the hierarchy, give each artifact one hero moment, treat whitespace as material, and make requested variants genuinely divergent. Each mode adds its own craft posture - slides think in sequence rhythm, motion thinks in beats with anticipation and one reveal, mockups respect platform conventions and touch targets, wireframes stay deliberately negotiable, documents read as typography with structure - each with concrete taste anchors. Real photography from Unsplash and Pexels joins the asset sourcing (downloaded into the project, never hotlinked), and a design that wants more typographic character may swap the seed's font tokens for a characterful pairing; motion compositions stay fully offline.

### Fixed

- **Windows commands run in the right PowerShell.** Agent commands on Windows were hardwired to `powershell.exe`, which is always Windows PowerShell 5.1 - a shell that rejects `&&`/`||` chaining, so every chained command a model wrote failed and had to be retried in single steps, burning time and tokens. Skales now runs agent commands in PowerShell 7 (`pwsh`) whenever it is installed and only falls back to 5.1 without it; on top of that, the agent is told exactly which shell it is in (in chat, Codework and the Telegram bot alike), so even on 5.1 it writes `;`-separated PowerShell commands correctly on the first try instead of learning it from errors. Linux setups also get their own platform hint instead of borrowing the macOS one, and the batch-folder shortcut in the prompt now shows Windows syntax on Windows.
- **Discover links that contain a `#` are clickable again.** A shared or auto-posted link whose URL had a fragment (the `#section` part, e.g. a Wikipedia section or an app deep link) had that fragment mistaken for a Discover hashtag: it was pulled out and highlighted as a tag, leaving the rest of the URL as dead, unlinked text. Feed post text now recognises URLs first, so the whole link stays one clickable link (opening in your browser) and only real standalone `#hashtags` are highlighted.
- **Backups no longer lose FTP passwords or stored secrets across machines.** The portable-secrets export covered the settings and connector keys but missed the two stores that encrypt in a different format (the internal secret store and saved FTP profile passwords); those travelled as the source machine's ciphertext and, with the encryption key deliberately kept local, silently vanished on restore to another machine. They now travel in the same portable form and are re-encrypted with the receiving machine's key; anything genuinely unrecoverable is reported instead of dropped.
- **The 1:1 Wrapped card is square again.** The share card labelled 1:1 was rendering as a cropped portrait: sized as a flex item, it was squeezed narrower than intended while its height stayed fixed, so the content overflowed and the corners were cut off, and the exported image inherited the same wrong shape. The card now holds its exact intrinsic size, the on-screen preview is scaled separately to fit the window, and the content is measured and scaled to fit the box, so 1:1 exports a true 1080x1080 square and 9:16 a true 1080x1920, with nothing clipped in any language.
- **Custom skills can be shared to Discover again.** The Skill AI generator added `require('fs')` and `require('path')` at the top of every skill even when the skill never used them, and the share screen rejects any skill that touches the file system. A share now strips those unused requires first (a require that is actually used still stays and is still screened), so a pure-compute skill is no longer blocked by dead boilerplate, and newly generated skills omit the unused imports.
- **Detect Ollama knows about Ollama Cloud.** With Ollama Cloud switched on, the Detect button and the failure messages used to tell you to start a local daemon (`ollama serve`), which is nonsense for the hosted service. They now point at your Ollama Cloud key and connection instead, and the provider is simply labelled "Ollama" (not "Ollama (Local)") since it can be either.
- **The Telegram bot names the real problem.** A network drop while polling (DNS failure, reset, timeout) was logged as "bot may have an invalid token", sending you to check a token that was fine. Network errors are now labelled as network errors (and recover on their own), and only a genuine rejection from Telegram is reported as a token problem.
- **API connectors that need two query credentials work now.** Some APIs authenticate with a public application id next to the secret key, both in the query string (Adzuna's `app_id` + `app_key` is the common one). The connector could only carry a single credential, so these always failed. A connector now has an "Extra query params" field for the public companion (sent in the clear, never encrypted, kept out of the model context), the secret query parameter's name is editable instead of a fixed `api_key`, and the docs importer recognises the two-parameter pattern automatically. On top of that, saving a connector with an auth scheme the request layer does not understand is now rejected with a clear message instead of silently sending an unauthenticated request that reads back as the API rejecting the key.
- **MCP tools get the time they need.** Every MCP request was cut off after a blanket 30 seconds, which aborted legitimately long operations (video analysis, renders, large scrapes) in the middle of their work. A tool call now has five minutes by default, each server can raise or lower that on its own up to 30 minutes, and the quick control requests such as connecting and listing tools keep their short timeout so a hung server is still detected fast.
- **Ollama reads images reliably, including Ollama Cloud.** Pictures sent to Ollama went through the OpenAI-compatible endpoint, which the hosted vision models answer with a server error; the same models describe the same image fine on Ollama's native chat endpoint. Skales now routes image turns over the native endpoint automatically - no setting, no URL to change - and text conversations keep exactly the path they had.
- **Claude over OpenRouter stopped re-paying its own prompt.** The direct Anthropic connection has long cached the static prompt prefix (tool schemas plus system prompt), but the same Claude model routed through OpenRouter sent no cache marker and re-billed that whole prefix on every turn. Claude models through OpenRouter now reuse their cached prompt the same way the direct connection does, so long conversations cost what they should; the volatile clock line stays outside the cached span so the cache survives minute boundaries, and every other vendor's request is untouched.
- **The OpenRouter speech model is actually spoken through now.** The provider card has offered a speech (TTS) model slot, but no read-aloud surface read it: chat read-aloud fell back to the system voice and voice notes only ever used the dedicated voice providers. With an OpenRouter key and a speech model configured, read-aloud in chat, WhatsApp and Telegram voice notes, Studio voice and the AIPointer overlay speak through OpenRouter first; an explicitly chosen voice provider (ElevenLabs, Azure, OpenAI, local) still wins.
- **Settings backups travel between machines.** Exports now carry secrets in a portable form while the local encryption key never leaves the machine; imports re-encrypt everything with the receiving machine's key instead of overwriting it, older backups migrate automatically, and anything unrecoverable is reported instead of silently dropped.
- **Thinking stays out of the chat.** Reasoning models (the DeepSeek, Qwen, GLM and MiniMax families among them) that spent a turn thinking could get their entire thinking transcript posted as the visible answer, in Flow a wall of inner monologue instead of the design file. The trace now stays on its own collapsed channel: a turn that burned its budget mid-think recovers with a re-request instead of dumping the transcript, an answer cut off inside its thinking block is treated as thinking, and the Flow live preview hides inline thinking while the model works. Plain helper calls on local reasoning-only models keep working as before.
- **The Flow window lost its redundant close button.** Flow runs in its own window, so the corner X only duplicated the window's own close. The project-files button moved into the preview and editor toolbars as the first icon of one right-aligned row next to the other actions.
- **The Flow composer got wider.** The attach button now sits above the send button in one column, so the message field takes the full remaining width.
- **The Start button looks like a button again.** Inside the Flow window it rendered without its styling; it is now the same accent icon button as the chat send button, and the scoping-questions Continue and Brand-Kit create buttons got the same treatment.
- **Editing a message keeps its picture.** Editing a question that carried an image resent only the new text: the image was silently dropped and the model answered blind. The edit now re-attaches the original picture from its saved copy (with the in-bubble thumbnail as the fallback), so a reworded question about an image still sees the image.
- **The new-chat page remembers your agent.** The agent picker on the chat start page reset to Skales on every visit, so anyone who mostly chats with one custom agent had to reselect it each time. The picker now remembers the last choice for the next visit. Skales stays the default character: nothing is stored until you pick someone else, picking Skales again clears the memory, and a deleted agent falls back to Skales.
- **Approving a phone-driven action replies to the right message.** When a chat run from the Skales mobile app pauses on a tool that needs confirmation (sending an email, pushing to git, running a command), the desktop asks the phone to approve or reject and, once you decide, sends the result back. That reply now carries the conversation's id through the whole approve-reject round-trip - the initial request, any follow-up approval, the final answer and the error path - so the phone pairs it with the exact message that was waiting instead of guessing by order, and a reply missed while the phone was briefly offline still settles cleanly. Local and non-mobile use are untouched.

## v12.1.0 - Flow

Design by conversation. Studio gets a new front door.

### Added

- **Flow, the conversational design workspace.** Open Studio and describe what you want; the Skales agent designs it as real files, with a live preview, the files and the code sitting next to the conversation. Eight modes cover the ground: slide decks, interactive prototypes, wireframes, mobile app mockups, print documents, generated images, generated videos, and motion graphics that render to a real MP4. Every mode carries its own design discipline, so the first result already looks deliberate instead of improvised. Flow is a beta.
- **Flow opens in its own window.** In the desktop app, Flow gets its own window so you keep working in chat and the other tools while a design generates. In the browser it stays an in-app overlay.
- **A composer that carries real controls.** Attach up to ten files (documents like PDFs become content sources the agent reads, not decoration), reference an earlier Flow project, pick a Brand Kit, pick a template, and choose the model and reasoning effort per project. The model picker searches the same live catalogs as the chat page, favourites and recents included.
- **Brand Kits drive the design, including what to avoid.** The Brand Kit gained fields for what the brand is, reference links the agent studies before designing, free design notes, and explicit bans: fonts, design directions and anything else that must never appear. Activating a kit in Flow makes the palette, the typography and the bans binding for every artifact, and a toggle makes the logo and uploaded brand assets a requirement, not a suggestion. New kits can be created right inside Flow; the settings page remains the full editor for the default kit.
- **Templates that shape the output, not just the prompt.** Picking a template (pitch deck, invoice, dashboard, logo reveal, ...) binds the right mode and injects a structural directive into every turn of the project, so a pitch deck follows a pitch deck's arc even five edits later.
- **Media generation with a choice of engine.** Image and video modes list every backend that is actually usable right now: connected MCP media servers, the configured cloud providers, local engines, and Skales Visuals, the built-in engine that designs images and videos itself, at the exact format you asked for, no external image model needed. Pin one, set aspect, resolution, quality and duration, and optionally name the model you want.
- **Generated images can be edited by talking.** Ask for a change after an image lands ("make the keyboard white") and the agent treats it as an edit of the existing file, through the image-edit tool or the media server's image-to-image, saving each revision as a new file.
- **Skills and connectors answer to "@" in Flow.** Typing "@" in a Flow composer lists your imported agent skills and connected MCP servers; a mention activates the skill or steers the turn to that server, the same way the chat composer does it.
- **Flow artifacts land in the shared gallery, and images can go to Discover.** Generated media is mirrored into the Studio gallery automatically, every turn shows a card of the files it produced, and any image artifact can be submitted to the Discover feed, compressed and reviewed before it appears.
- **Discover can now carry video posts.** The feed renders link-based videos with a muted inline player that respects the clip's format, moderated the same way as images.
- **OpenRouter carries every modality now.** One OpenRouter key no longer means text only: the provider card gained per-capability model pickers, fetched live from the catalog and filtered to what each capability actually accepts, for image generation, video generation, music, speech (TTS), transcription (STT) and embeddings. Image models join Studio and Flow as a first-class engine, video models generate through the async job API with honest progress and errors, voice input can transcribe over OpenRouter, read-aloud can speak through a dedicated speech model with a voice of your choice, and the semantic document index can embed through OpenRouter as well.
- **Skales IQ inherits the same breadth, tier by tier.** Skales IQ now understands every modality (chat, vision, image, video, music, speech, transcription, embeddings), and what your plan can do is managed per tier and can grow without an app update. A capability that is off or not in your tier answers with a clear message in chat instead of a cryptic failure, the same way an offline Skales IQ already does.
- **Drag and drop lands in ongoing chats.** Dropping a file onto the composer of a running conversation now attaches it, PDFs through text extraction, images to the vision slot, text inline and archives to the Workspace, exactly like the paperclip. The starting page accepts PDFs and archives too, and a PDF that cannot be parsed still attaches with a Workspace copy instead of being skipped.
- **Flow asks before it builds.** On the first turn of a project, when the brief leaves essential decisions open, the agent poses a handful of tailored scoping questions rendered as a clickable form in the preview pane: option chips, multi-select where several picks make sense, a free-text field and "Decide for me" per question. Continue sends the picks back and the artifact is built to them; a clear brief skips the questions entirely.
- **A template gallery on the Flow home.** All twelve templates fan out as cards under the composer; hovering one previews its brief right in the prompt box, clicking binds the matching mode and structure, and a blank-project link sits below.
- **Change course mid-project.** The Flow workspace header carries a small toolbar: switch the model (with live catalog search) or switch and deactivate the Brand Kit for the next turn, without leaving the project. The workspace composer gained an attach button too, so fonts, images and archives can join a running project, and a live token counter shows what a run actually costs.
- **Skales unpacks zip archives.** A new extract tool unzips archives natively (no shell), guarded against path escapes, available to the agent in chat and everywhere else. A zip attached in Flow is unpacked automatically into its own assets folder.
- **Ollama Cloud, one switch away.** The Ollama card gained a Cloud toggle in its own row: flip it, paste your ollama.com account key, and Fetch Models fills the regular model list with your hosted models, no local install involved. Detection doubles as a cloud connection check, the local-install sections (setup, marketplace, the local tool cap) step aside in Cloud mode, and the hosted frontier-size models are treated as the cloud models they are, with none of the local limits.
- **Your OpenRouter choice leads.** When OpenRouter is the active provider or one of its modality models is pinned, image generation, voice transcription and read-aloud speech go through OpenRouter first; the other configured providers remain fallbacks.

### Changed

- **The classic Studio surfaces moved behind one "Studio v1" group.** Design, Media, Audio, Type, Scenes and Gallery keep working exactly as before, folded into a collapsible rail entry below Flow while Flow is the front door.
- **The Studio landing for Flow is honest about readiness.** One column: the checks on top with a wide open button, the explanation below. Configured MCP servers count as ready (they connect on first use), and the media row mirrors, key for key, what the image and video pipelines actually accept.

### Fixed

- **Vision Provider with OpenRouter returned a 404.** OpenRouter needs vendor-prefixed model ids, and the vision path sent the bare id the settings carried. Bare ids of known families get their prefix automatically now, switching the vision provider prefills that provider's known-good default model instead of carrying the previous one along, and the model field shows a provider-aware example.
- **A text-only local model no longer silently becomes llava.** When the active Ollama model cannot see images, Skales now looks at which vision models are actually installed and uses one of those; with none installed it says exactly that, with the pull command, instead of failing with a confusing missing-model error. Detection also recognizes cloud-suffixed and differently-spelled tags of the multimodal families.
- **A #trigger routes to its API connector, deterministically.** Typing a connector trigger in chat now pins the turn to that connector instead of hoping the model picks it over web search or an MCP tool, a trigger saved with uppercase resolves too, and a connector whose docs yielded no endpoints says so in Settings instead of failing quietly.
- **The command highlight keeps its place.** When the colored command overlay appears mid-draft in a scrolled composer, it aligns to the text in the same frame instead of showing the caret on the wrong line, in the chat session and on the starting page.
- **The reasoning-effort tooltip is shorter.** The dial says what level is active without the click-to-cycle lecture.
- **The per-modality model pickers actually fetch now.** A key-handling fault made every fetch quietly fall back to a static text list, so the pickers looked empty. Fetching works now, the public catalog even loads without a key, and the pickers moved from a native browser datalist onto the app's own dropdown, loading their catalogs automatically when the panel opens.
- **PDF attachments work in the packaged app, not just in development.** Packaged builds shipped without the PDF text extractor, so every extraction failed silently while development worked fine. The packaged app now carries it.
- **Long generations are never cut off anymore.** The per-request timeout used to stay attached to a streaming response, so a model that was actively writing a large file for minutes was aborted mid-work with a timeout error. The timeout now only bounds the wait for a response to begin; once a model streams, only genuine silence on a dead connection ends a run, and the Stop button remains yours.
- **The Flow chat can be read while the agent works.** Auto-scroll only follows the newest message when the view is already at the bottom, instead of fighting an upward scroll.
- **Fixed-format compositions fit the preview.** A 1920x1080 motion graphic (or a 9:16 reel) scales to fit the pane instead of running off screen, and Motion gained format and duration controls that the composition and the renderer respect.
- **Flow projects stay out of the chat history.** Flow sessions no longer appear as regular chats in the sidebar, History or the command palette; they reopen from Flow's own project grid.
- **Small but annoying, fixed in Flow.** The code editor no longer collapses to a strip, a long model name no longer pushes Start to the next line, cloud-generated images and videos show real thumbnails in the project grid, the mode chip rail lost its stray dark box, and the Flow window in the desktop app wears the same hidden-inset chrome as the main window.
- **Flow works on a narrow screen.** Opened from a phone browser (Tailscale on the go), the workspace stacks chat above the live preview instead of hiding the preview, dropdown menus clamp to the viewport, and the template gallery wraps into rows. On desktop, the gallery breaks out of the column so no card is cut off and the hover lift is never clipped, and the mode chips shrink to fit one line.
- **Picking a template no longer types into your prompt.** A chosen template shows its brief as the placeholder (exactly what hovering previews) instead of inserting text that survived switching or clearing the template; with the box left empty, Start uses the template's brief as is.
- **LLM Profiles no longer flatten Flow designs.** Profiles keep weaker models precise while they operate tools, which is right for ordinary work but wrong for design: DeepSeek and MiniMax were composing Flow layouts in that precise mode, and the results came out flat and generic. Flow now keeps the model's full creative range while it designs; everything else stays precise.
- **Agent-written documents reach the Document panel again, proactively.** Documents the agent wrote were invisible to the panel, a fresh chat never told the model the panel existed, and weaker models wrote documents as plain chat text instead. All three fixed: ask for a summary, article or report in any new chat and it lands in the panel, opens rendered on a wide window, and lights the header dot on a narrow one.

## v12.0.0 - Solid

Two steps ahead. (This entry covers the work landed so far; the release is still in build.)

### Added

- **A format for role bundles.** Skales now has a defined format for a role bundle: one folder that carries the skills, connectors and slash-commands for a job role, with a loader that validates it before anything uses it. A bundle is only sound if every connector it ships is reachable through a skill or command, so a role arrives complete instead of as a connector with no skill that uses it. There is no install screen yet; this is the groundwork, with an example bundle in the developer kit.
- **Skales learns from your finished work, and the learning is yours.** When a goal hits friction and finds a way through, Skales keeps what worked as a short, reusable approach and notes what to avoid, so the next job of that kind starts ahead instead of from scratch. The approaches it leans on get sharper the more you work, since it favours the ones that actually reach a good result and quietly drops the ones that do not, and a quick "that's perfect" or "that was wrong" on a finished goal counts. The memory page shows each lesson with a quality mark, lets you pin the ones you rely on, and delete anything you do not want kept. It is a plain file on your computer that you own and can read.
- **Tell Skales to remember.** Say "remember this" or "from now on...", and it keeps that as a first-class note that surfaces first the next time it is relevant.
- **Skales stays a step ahead.** It surfaces a meeting that is about to start or a scheduled task that did not run, quietly prepares for a meeting that has an agenda before you ask, and gathers everything else into one calm daily briefing instead of a stream of pings. The memory page shows what it got ahead of and why, and you can mute it in notification settings.
- **Skales names your chats for you.** A new conversation gets a short, specific title drawn from its first exchange, the way you would name it yourself, instead of the first line of your message. The placeholder appears at once and is replaced when the title is ready.
- **Show an image you already have.** Skales can display an existing image file from your workspace right in the chat, so a picture you or a connected service saved appears inline instead of being described or pasted as raw data.
- **A dot shows when a document is waiting.** The document button in the chat header now carries a small mark whenever the current chat has a document but the panel is closed, so an artifact Skales wrote, or one in a chat you reopened, is easy to find instead of hidden behind a closed panel.
- **Delete your Skales IQ trial data yourself.** A new control in Settings, under Skales IQ, removes your trial record from our server and switches off its key in one click. The signup now also says plainly why your email is kept, to prevent abuse and to offer you a later upgrade or credit, and that you can remove it whenever you want.
- **Codework templates are back in the library.** The Templates page has a Codework category again, with ready-made coding tasks, fixing a bug, writing a test suite, refactoring, scaffolding an API endpoint and more, that open straight in Codework with the task already filled in.
- **Find your real models, not a fixed list.** The model switcher now shows the models you actually have. Your favourites and recently used sit at the top, and a search field finds any model across every provider you use, your real OpenRouter, Gemini, Anthropic and Ollama models, instead of a short hardcoded lineup. A star keeps any model one tap away, the same in the in-chat switcher and on the new-chat screen, and a local model server that is not running drops out instead of listing dead entries.
- **Pick who answers and which model before you start.** The new-chat screen has its own row under the box for the agent and the model. Choosing an agent fills in its model for you, and you can switch to any other model just for this chat without changing your default. The choice sticks to that conversation and is still there when you reopen it, and a later switch inside the chat sticks too.
- **A new in-between coding mode for Chat.** Between Code, which asks before each edit, and Auto, which works on its own, there is now an Edits mode: it approves your file edits as it goes but still asks first before a shell command, a git push or a deploy. Pick it from the same Chat, Code, Plan, Auto strip.
- **Plan mode hands you the plan to approve.** A folder-bound chat in Plan mode now writes out its finished plan and waits for you to approve it before anything runs, instead of switching to Code and starting on its own. You read the exact plan, then approve to build it.
- **Choose how much Codework may touch.** A new file-access setting runs a project read-only, lets it edit, or lets it edit but never delete. Deleting a file always needs an explicit yes, whatever the setting.
- **Chat reads your project's own rules.** A folder-bound chat in a coding mode now reads an AGENTS.md (or CLAUDE.md) at the folder root and follows it, so your project's style, test runner and conventions carry into every reply without you repeating them.
- **Codework remembers your project across runs.** Before it starts, Codework reads an AGENTS.md (or CLAUDE.md), memories.md or branding.md at the project root and follows it, so its work keeps to your conventions and tone every time instead of relearning them.
- **Codework tells you when it is done.** A finished or interrupted Codework task now raises the same toast, chime and sidebar mark as a chat, so a task you left running reaches you even when you are on another page.
- **Your activity recap covers the tools you actually reach for.** Using an MCP tool or a Hugging Face Space, creating a template, and exporting any Studio video (not only a text animation) now register in your Discover activity and your weekly Wrapped, instead of going unrecorded. Wrapped also keeps the model you leaned on most for the week and tells your Chat, Code, Plan and Auto time apart.
- **A home you choose: Calm or Power.** The dashboard opens in one of two layouts and remembers which one you picked, so it looks the same the next time you open it. Calm keeps it personal: pick up where you left off, jump back into recent chats, what Skales has on its mind, your most active surfaces, and quick ways to start. Power is for getting work done: your active goals with their real progress, Autopilot status with this hour's API calls and anything waiting on your approval, your provider balance, your connections and paired devices, and live runtime memory, CPU and uptime. Your own avatar emoji greets you, the current weather sits under the greeting, and your weekly Wrapped badge rides in the header with a tap straight to the recap.
- **Your briefing, in a single line under the greeting.** When you have joined a Briefing in Discover, its latest items rotate through one calm row and open in the built-in browser on click. When you have not joined yet, that same row invites you to, so the home screen is where a briefing begins.
- **A balance you can trust, or nothing at all.** Running on Skales IQ or OpenRouter, the Power view shows your real remaining balance with a gauge. Skales IQ shows the percentage of your trial left, exactly like the Settings box; OpenRouter shows your remaining credit in dollars. For a subscription or a key that exposes no balance, the card simply does not appear, rather than inventing a percentage.
- **Your week, at the top of the home screen.** A seven-day strip under the greeting lays out the current week, with the events and planner tasks for each day, and today marked. Empty days stay empty rather than guessing. It is there in both Calm and Power, and any day opens the Planner.
- **Pick up where you left off, in both views.** The one-click way back to your last session now sits at the top of Power too, not just Calm, so getting back to work never means hunting through History.
- **Real runtime health, measured not guessed.** The Power view reads the backend's actual memory, CPU and uptime live and labels them as the runtime's own, instead of the browser-heap placeholder the old system tile showed. Nothing on the home screen is a stand-in number.
- **Home widgets that all show real data.** The optional-widget strip gains your latest Studio work, your running goals, the next scheduled goal, your latest Briefing items, your Wrapped badge, your online team devices, and your most-used surfaces. The empty Quick Chat box, the duplicate memory cloud, the buddy-mood tile and the placeholder system tile are gone.
- **Choose how deeply the model thinks, per chat.** A small dial next to the message box steps through four levels of reasoning effort, and its circle fills and shifts colour as you go up: an empty yellow-green ring for the lightest, then half blue, a full orange, and a full red for the deepest. The dial follows the model you have picked. On models with a real reasoning control, Claude Sonnet 5 and the newer Opus, and reasoning-capable models through OpenRouter, it drives that control directly. On models that have none, your local models, custom endpoints and most direct providers, it shows a muted "none" ring and steps aside, so it never claims a depth the model cannot deliver and the message box never shifts when you switch models. The level stays with that conversation, and the dial is on the new-chat screen too, so a level you pick before the first message rides into the session. Once you raise it, it takes over from the global deep-reasoning switch for that chat; a fresh chat, or the dial back at its lightest, follows your global setting.
- **Claude Sonnet 5 is ready to use.** Sonnet 5 is in the Anthropic model list and runs with its adaptive thinking on by default, with its output length and long-conversation compaction sized for it, so a long answer no longer cuts off early.

### Changed

- **Trivial turns feel instant.** A greeting, a thanks or a quick "ok" skips the heavy setup a real task needs and answers right away, with no thinking card sitting above a one-word reply.
- **Deep reasoning reaches more of your work, and stops wasting itself.** With the xhigh deep-reasoning boost turned on, a Codework run now plans as carefully as a chat or a goal already did, so a coding job gets the same care. And a greeting or a quick thanks no longer triggers a full reasoning pass, so those pleasantries stay instant.
- **The reply box frees up the moment your answer lands**, instead of waiting on background bookkeeping, so you can keep typing without a pause.
- **A tidier message box.** The magnifying-glass and slash buttons are gone from the message box. Both only opened things you already reach by typing "/", which lists every command as you type, so the reasoning-effort dial takes the slash button's old spot. The small labels on the message-box buttons now appear the instant you hover instead of after a pause.
- **A document Skales writes opens ready to read.** It opens rendered, the finished look, instead of as raw markdown; switch to the editor when you want to change it.
- **It feels like a native app, not a web page.** The pointer is the system arrow over buttons and links instead of the web hand, and every dropdown is a real, keyboard-friendly menu that matches Skales rather than the raw operating-system control.
- **Clearer empty and error screens.** When a list has nothing in it yet or something fails to load, Skales shows a short explanation and a way forward, like try again, adjust filters or start a chat, instead of a bare spinner or a raw error line.
- **Every tile on the home screen goes somewhere.** Each card, stat and list on the dashboard now opens the thing it stands for, a chat, a goal, a memory, a setting, the schedule, the briefing, instead of sitting there as text. The old capability grid that led nowhere is gone.
- **Your recap counts the work you do outside chat too.** Finishing a Codework project, running an Organization, holding a Group Chat, running a custom skill, and rendering a Studio video now register in your weekly Wrapped and the dashboard's most-active card, the same way in-chat work already did, so the picture of how you use Skales is no longer chat-only.
- **A cleaner, more human voice.** Replies avoid em-dashes and marketing filler, both in how Skales is guided and in a final pass over the answer.
- **A smaller, faster download on Mac and Linux.** The installer no longer carries build tooling it never runs, so it is meaningfully smaller and updates arrive faster.
- **Background and team work runs leaner.** Unattended tasks, agents and team plans load only the tools a step needs instead of the whole set.
- **The free trial no longer ties itself to usage analytics.** When you start Skales IQ, anonymous usage statistics stay on by default to help improve the product, but onboarding now has a clear switch to turn them off on the spot, and the trial starts either way. Choosing not to share them never blocks or changes your trial.
- **The native, consistent look now reaches the rest of the app.** Settings, Chat, the sidebar and History share the same rounded cards and inputs, snappier motion that only animates what should move, the last raw dropdowns replaced by the same keyboard-friendly menu, and disabled controls that dim again, so the whole app feels of a piece. History's provider and channel filters sit on one tidy row with the refresh beside the conversation count, and the sidebar's bottom bar reads more calmly.
- **The new-chat screen has more to say.** It opens with a wider, rotating set of lines, including a few meant to make you smile, instead of the same handful, and the input keeps a little breathing room when you focus it.
- **Codework asks before writing by default.** New file writes wait for your approval until you say otherwise; turn auto-approve back on in the approval panel whenever you want the faster flow.
- **Full disk access takes a deliberate second step.** The full-disk option in the Code access prompt now says plainly that it applies to every chat and stays on until you change it in Settings, and asks you to confirm once more, so a single tap can no longer open your whole disk everywhere. Granting just the one folder stays one tap and is the recommended choice.
- **Codework follows the same folder-safety rules as the rest of Skales.** It uses the shared protected-paths list and re-checks the real location of a file before writing, so a project that tries to reach outside its own folder through a link is stopped.
- **Codework works to your full step budget, not a fixed limit.** A run now follows your goal step-budget setting all the way (0 means run to completion, like a goal) instead of a hardcoded ceiling, so a real autonomous job is no longer cut short after a handful of steps; a stuck model is still stopped early by the progress guardrails.
- **Codework's autonomy settings moved out of the header and onto the start screen.** You now set auto-approve, file access, preview and the test command before a run, on the start screen, the way a coding agent should. During a run the gear opens those same settings as a clean centered panel that matches the rest of Skales, instead of a strip wedged into the header that could sit on top of a pending approval.
- **Codework's approval looks like the one you know from chat, sits where you are working, and waits for you.** A pending action now shows as the same clear approval card you see in chat, with Approve, Deny and Always approve this session. It is pinned to the bottom over the terminal, not jammed into the header, so the step it is asking about stays readable above it. "Always approve this session" now actually gives the run a free pass, so the rest of the run stops asking instead of prompting again on the next command. And it is patient: the approval waits 30 minutes instead of 5 and a run may take up to an hour, so pausing to read it or opening settings no longer aborts the task with a timeout.
- **Codework reads and searches your files through the same engine as the rest of Skales.** Reading a file and searching the project in a Codework run now go through the shared tool path that Chat uses, so an improvement or fix to it reaches both at once and there is one set of file-safety rules to trust. Writing files, running commands, the file tree, diffs, the write preview and undo are unchanged, and a run still works only inside the folder you chose.
- **Auto pauses before it reaches outside the work.** In Auto mode, an action that pushes to a remote, deploys, runs a destructive command, or writes outside the folder you bound now stops for a single approval instead of going through unattended. Ordinary edits and commands inside the folder still run without asking, so Auto stays fast where it is safe and checks in only where the stakes are higher.
- **Codework shows an accurate diff, and you can open any file to read it.** The change view now lines up moved code correctly instead of marking every line after an inserted one as both removed and added, so a real edit reads cleanly. Clicking a file in the tree opens it in a read-only viewer, so you can check the code on the right without leaving the page. It stays a viewer, not an editor: Codework works by doing, not by hand-editing.
- **A coding chat gets the same project map as Codework.** The structural index of your project now uses the same, larger budget in Chat as in Codework, instead of a tighter chat-only cap, so on a big codebase the model starts with more of the picture. When the map is still too large to fit, it says so and points the model at the rest to open on demand, rather than quietly handing over a partial map.
- **A mistyped file mention tells you, instead of failing quietly.** In a coding chat, when you mention a file with @ that is not in the bound folder, Skales now says it was not attached, so a typo no longer looks like it was simply ignored. A bare word that is not a path, like a skill mention, is left alone.
- **Safe commands stop asking, risky ones get a clearer prompt.** A short list of read-only and test commands, like npm test, git status and listing files, now runs without a confirmation, so a command you repeat all day stops nagging. This skips the prompt only inside a folder-bound coding session (Code, Edits or Auto on a folder you chose); a plain chat still confirms every command. Installing packages or pushing to a remote now asks with a message that says what it does, instead of a blanket "Run command?". A force-push that rewrites remote history, or a download piped straight into a shell, is refused outright with an explanation. The block on system-wrecking commands is unchanged and still cannot be turned off.
- **Commands Skales runs no longer see your secrets.** When Codework or a coding chat runs a shell command, the command now runs with API keys, tokens and Skales' own internal values stripped from its environment, so a script in a project you are working in cannot read them straight out of memory and send them away. The command still runs only in the folder you chose, and new file writes still ask first unless you turn that off.
- **A folder-bound run cannot read outside its folder through a link.** When a coding run reads a file in the folder you chose, it now checks the file's real location, so a link inside the project that points somewhere else can no longer be used to read a file outside the folder. The write side already did this; the read side now matches.
- **Codework reads several files at once.** When Codework decides to read or search more than one file in a single step, it now does that reading in parallel, up to four at a time, instead of one after another, so the part where it gathers context to understand your code is quicker. Writing files, running commands and everything else stay one step at a time, in order. A step that mixes a read with a write or a command runs sequentially too, so a read never returns content from before a write in that same step.
- **Your most-used tools live in the main menu now.** Workflow, Planner, Tasks and Wrapped moved up from Tools into the main sidebar, Discover sits right under History, and Codework moved into Tools, so the surfaces you reach for every day are one click away. The order is the same in every theme, and anything you have switched off in Add-Ons still stays out of the sidebar.
- **Features that have settled no longer wear a Beta tag.** Teams, LLM Profiles, Agent Swarm, the Always-On Agent, Call Mode and memory Dreaming have been steady for a while, so their Beta labels are gone, in every language. The genuinely early ones, like the Skales IQ trial and the Business preview, still say so.
- **The home screen fills in faster.** The dashboard now runs its status checks together instead of one after another, holds the weather and your goal and memory lists for a few seconds, and no longer scans the same files twice on a single open, so it settles quicker without going stale, and a memory you delete still disappears at once.
- **The update screen puts its links last.** The social links now sit at the very bottom of the update page, below the updater's own log, so the log is easier to reach.

### Fixed

- **A connected tool cannot smuggle hidden instructions into Skales.** A tool from an MCP server is third-party, and its description is shown to the model, so a malicious or careless server could hide commands in invisible or control characters inside that text. Skales now strips those characters from every MCP tool description and caps its length, so a connected tool cannot quietly steer the model through text you never see.
- **One misbehaving MCP tool no longer breaks the rest.** A tool that reports a malformed input schema is now coerced to a safe shape instead of breaking the model's tool-calling for the whole message, so a single bad server can no longer take the others down with it.
- **Skales works with MCP servers on the newer protocol.** It now remembers the protocol version a server agrees on and sends it back on every later request, the way the current spec requires, so a stricter or newer server no longer rejects Skales' calls after connecting.
- **The balance card shows a number you can place.** On OpenRouter the home screen now shows only your real remaining credit, without the confusing lifetime-total figure and gauge beside it that did not map to any budget you set.
- **A stopped background engine comes back on its own.** If the Skales server stops while the app is open, for example because its background helper was force-quit (a common way to close Skales on Windows, where shutting the window only hides it to the tray), Skales now quietly restarts it and reloads you right where you were, instead of a red "stopped unexpectedly" box. A calm restart prompt appears only if the engine cannot stay running after several tries.
- **On Windows, selecting Ollama no longer freezes the app or flashes a console window.** If Ollama is not installed Skales says so at once instead of opening a stray window and hanging for ten seconds on every check; when it is installed it starts quietly with no window.
- **A model that got stuck loading its tools now just works.** Some models would keep announcing that a tool is "available next step" instead of using it; Skales now hands those models every tool directly and adds a switch under Settings, Advanced to turn on-demand tool loading off if you ever run into it.
- **The memory page reads cleanly again.** The nightly Dreaming summary and the knowledge-graph line showed a placeholder instead of the real count, and Dreaming could list raw bits of page data as if they were facts. The counts now read normally, only real sentences are kept as memories, and any old stray fragments stop showing.
- **Studio templates fill themselves in.** Picking a template for Skales Studio now opens it with the prompt already in place on the right tab, instead of an empty screen.
- **What you set about yourself stays yours.** The emoji, occupation and goals you enter on the Memory page no longer get overwritten when Skales does its background tidy-up of what it knows about you. The cleanup used to let its model rewrite those user-set fields with its own guesses; now your own answers are kept every time, while a field you left blank stays open for Skales to fill in as it learns. Only the AI summary is meant to update on its own.
- **Plan mode is genuinely read-only.** It used to let a writing connector or an external tool slip through because they were judged only by their name; Plan now reads what the tool would actually do, so a chat set to Plan investigates and proposes but changes nothing until you switch to Code.
- **Codework approvals are steadier.** A pending approval or a proposed change is now also saved to disk so it is easier to keep track of in the background, and a stale internal reference that could send a tool down the wrong path was removed.
- **A long chat that becomes a goal now proves it is finished.** When a long task quietly turns into an autonomous goal, Skales works out what "done" means for it and checks against that before saying it is finished, instead of taking the model's word, so a weaker model can no longer stop a converted task early.
- **Codework re-checks the project folder before it reads anything.** The folder you point Codework at is now validated on the server as well, not only in the window, so a run always works inside a vetted, writable, non-system folder before any file is read.
- **Plan mode catches more external write tools.** A connected tool whose name mixes a read word with a write one, like a "query and delete" tool, is now held back in Plan mode instead of slipping through on the read word, so a read-only investigation stays read-only.
- **Wrapped reports an honest "most-used tool".** Studio, Organization, Playbooks, Templates and MCP activity used to fall into the Chat bucket, which inflated Chat and hid those tools; each is counted on its own now. A busy week is no longer trimmed early either, so the activity total reflects everything you did instead of stopping at an old internal limit.
- **A shell command cannot slip a second action past the safety check.** A safe command that runs without asking in a coding session can no longer carry a riskier one hidden on a new line, and Auto mode now pauses for a single approval on a destructive command however its flags are spaced or spelled, and on a command that writes to a file outside the folder you bound. A force-push that rewrites remote history is still refused unless you have deliberately set safety to Unrestricted.
- **A coding run stays inside its folder when it reads, not only when it writes.** A file reached through a link, or through a path that points up and out of the project, is no longer read from outside the folder you chose, so the read side now matches the protection the write side already had.
- **The commands Skales runs never see your secrets, including the ones it runs for itself.** The internal version, status and search commands now also run with your API keys, tokens and Skales' own values stripped from their environment, and a few more credential shapes (a database URL, a webhook) are recognised and removed.
- **A long chat that becomes a goal finishes in one go again.** A task that quietly turned into a goal at the step cap could get stuck asking you to keep going instead of completing on a clear, finished answer. It now completes unless a real check actually finds something unfinished.
- **Your daily briefing still reaches you when the first look of the day was quiet.** If nothing was waiting at the first morning check, the day stays open so anything that comes up later still arrives in one calm briefing, instead of the slot being spent on an empty one.
- **One reminder for a meeting, not two.** With a check-in style turned on, a meeting about to start no longer pings you twice from two different places.
- **Skales keeps only what you meant to teach it.** A one-off request phrased like an instruction ("from now on, can you...") is no longer stored as a permanent rule, and pinning or deleting a saved lesson on the memory page can no longer collide with the background learning and lose your change.
- **A new chat is never named "No response", and a real title is kept.** A title that happens to begin with "We", "I" or "The" is no longer mistaken for leaked reasoning and dropped, while an empty or thinking-only reply no longer becomes the chat's name.
- **Every most-used surface on the home screen opens its own page.** A row for Codework, Studio, Organization, Playbooks or Templates now opens that surface instead of always opening Chat.
- **Premium replies stay quick across a long session.** Replies from Claude reuse the stable part of the prompt from one turn to the next again, so a long conversation does not slow down as it grows.
- **Role bundles load safely.** An empty or malformed bundle file no longer stops the rest of a bundle from loading, and a connector named with a scope or a symbol is recognised correctly.
- **The dark and light mode control is labelled correctly** again, instead of reading "Appearance".
- **Typing a command in a long message keeps the cursor where you are.** When your draft held a /command or an @mention and grew tall enough to scroll, the box could stop following your cursor and drop your taps in the wrong spot, so you could not fix a word. The box now scrolls with you and the highlighted text lines up with the caret, so a long message edits normally.

## v11.6.0 - Bedrock

A deep reliability pass across everything Skales does. Tools now do the work they claim and tell you the truth the moment something fails, instead of reporting a confident "done" that quietly did nothing. This is the solid engine the next release builds its new look and feel on.

### Fixed

- **Tools tell you the truth about whether they worked.** Across messaging, calendar, WordPress, Notion, YouTube, smart home and more, an action that failed, was not configured, or only partly succeeded used to come back as a cheerful success. Skales now checks the real result and reports an honest failure with the reason, so you find out the moment it happens instead of discovering later that nothing was sent, saved or posted.
- **Messages reach the right person, every time.** WhatsApp now matches a contact exactly and asks rather than guessing, so a message never goes to the wrong person from a partial name or number. Telegram only reports "sent" when the send truly went through. Long Discord messages are split into parts instead of being silently cut off. Twitter mentions and your timeline show up again, and Signal correctly reports a failed delivery instead of claiming success.
- **Email is safer.** A reply now goes through the same trusted-address check as a new email, finds the original message in any folder, and uses the right account. Deleting one email removes only that message instead of risking a sweep of others in the mailbox.
- **Reminders and scheduled goals are dependable.** A reminder set for later no longer fires the instant you create it, and a reminder whose delivery hits an error is retried instead of being marked done and lost. A scheduled goal whose previous run crashed now recovers and runs, instead of skipping itself forever. An empty or malformed schedule is refused instead of creating a job that does nothing.
- **Long tasks and big projects finish.** A goal that writes many files no longer stops partway at a hidden limit. When a step is cut off by length, the parts that were valid still run and the rest is retried, instead of the whole step being dropped or run with empty content. A tool waiting for your approval is no longer mistaken for a failure that aborts the task.
- **Goals run autonomously to completion instead of asking you to babysit.** A goal now works the whole task through on its own and stops only when it is done, when it genuinely needs your input, or before a consequential action like sending an email or deploying, where it still asks once (with a one-tap "always allow" on the card). It no longer pauses every dozen steps for a "Continue?" card. A plain chat that grows into a real multi-step task is carried on as a goal automatically and finishes in one go, instead of stopping partway with a "parked as a goal, continue?" card. And a goal that did reach its limit now picks itself back up while you are away, so it keeps moving without you. The step limit in Settings > Goals is now a safety ceiling against a runaway task, not a check-in cadence, set high enough that normal work never reaches it; 0 means run to completion.
- **Replies do not freeze, and thinking models finish their answer.** If a provider sends the end of an answer and then holds the line open, Skales keeps the finished answer instead of throwing it away. Reasoning models are given the time they need before Skales decides a reply has stalled.
- **Claude and Gemini type their answers out as they go.** Replies from Anthropic (Claude) and Google (Gemini) now stream in word by word as they are written, the same as every other provider, instead of sitting on a blank screen until the entire answer (and any thinking) is finished. The two premium providers were the only ones that made you wait for the whole thing, so this is where the wait for the first word was longest.
- **The same behaviour across every AI provider.** Tool use, sending an image by file, parallel tool calls and reasoning-model requests now work consistently whether you run Gemini, an OpenRouter model, Kimi, DeepSeek, Anthropic or a local model, instead of one provider quietly behaving differently. A tool action requested over WhatsApp or Telegram now runs reliably instead of stalling the reply.
- **Your files stay inside your workspace.** Editing, moving and copying a file now resolve relative paths inside your workspace instead of escaping to the install folder, binary files are no longer read as text, and a saved session can no longer be opened through a crafted path.
- **Web access is safer and actually returns the page.** Built-in web fetching is routed through the same protection that blocks internal and local-network addresses, and a fetched page returns its real content to the model instead of only a character count.
- **Media gets delivered.** A generated image arrives on Telegram instead of a broken reference, a video scene actually renders into the result, a voice clip is returned even when Telegram is not connected, casting a link with special characters works, and a browser playbook reports a failed step instead of marking everything done.
- **Connected services hold up.** Google Drive and Docs refresh their sign-in automatically instead of breaking after about an hour, Notion reports a real failure on invalid input, Home Assistant validates a command and confirms the change actually happened, and a custom capability you add survives the next rebuild instead of being wiped.
- **Your memory and goals do not get corrupted.** The knowledge graph and goal schedules are written safely, so a crash or two changes at once can no longer empty or scramble them, and a partly-failed update is reported as exactly that.
- **Skills are honest about themselves.** Creating a skill now verifies it actually loads before claiming success, deleting one matches it safely, and the built-in documentation opens correctly in the packaged app.
- **Your message always shows, even when you send the same thing twice.** Sending an identical message again no longer makes its bubble disappear from the chat after the reply arrives.
- **The chat is ready the instant the answer lands.** The message box re-enables as soon as the reply is saved instead of waiting on background bookkeeping, and a long conversation stays smooth to type in, so you can keep going without a pause.

### Changed

- **Skales IQ, the free built-in trial, is more private and more reliable.** Every trial request now enforces zero data retention at the provider, the trial no longer garbles ordinary words like "Google" in its answers, and its daily limit survives a server restart. Activating the trial no longer turns on usage analytics on its own.
- **Approvals reach you wherever you are, and the task finishes after you approve.** A sensitive action started from your phone now asks for your approval and waits for it, then carries on with the rest of the task once you approve, instead of stopping at the approved step and dropping whatever came next. Writing or skill-authoring actions are held back in read-only Plan mode and ask first over WhatsApp.
- **Simple turns stay snappy.** A greeting, a thanks or a quick "ok" gets a fast, light reply, and one earlier hiccup no longer makes the rest of the conversation heavier.
- **More connected integrations without the slowdown.** Having many MCP servers, custom skills or Hugging Face Spaces enabled no longer weighs down every message, so you can keep more of them switched on at once without each turn getting heavier. Disabling an integration still removes it entirely.

## v11.4.62 - Polyglot

### Fixed

- **The gecko shows on the startup screen again, even on a large profile.** On a machine with a lot of saved chats and data, the little gecko on the loading screen could come up blank while everything loaded. The startup and error screens now carry the gecko with them instead of fetching it, so it always appears right away.
- **Kimi models run their tools again, and stop leaking their thinking into the chat.** On Kimi (Moonshot) K2 models, including the thinking variant, a tool the model wanted to use was sometimes written into the reply as raw internal markup instead of being run, so nothing happened and the markup showed up in the chat. Skales now recognizes that format, runs the tool, and keeps the visible answer clean.

## v11.4.61 - Hotfix

### Fixed

- **Long tasks no longer freeze when a model goes quiet mid-reply.** If a provider stops sending partway through an answer, Skales now notices within seconds, starts the reply over and switches to another provider if one is set up, instead of sitting frozen for minutes. A task that stalled this way keeps going on its own.
- **Big files stop spinning and finish.** When a single file was too large to write in one step, a task could keep retrying the same oversized write and use up its whole budget without finishing. It now writes the file in parts and, if a model still cannot, stops with a clear message instead of looping. The same fix applies to the desktop Buddy.
- **A thinking model gets enough room to finish its answer.** Reasoning models are no longer squeezed down to a tiny reply size, so they finish the answer instead of thinking and then going silent.
- **An action cut short by the output limit is retried the right way.** Actions other than writing a file, like updating a task list, are no longer pushed into writing a file by mistake when they get cut off; they are simply tried again more compactly.

## v11.4.60 - Scenes & No Cap

### Added

- **Scenes: a real multi-scene video editor in Studio.** Describe a video and get a scene-by-scene plan, then edit and regenerate each scene, pick a visual style, reorder scenes and drop your own image into any of them. Watch a live preview of a single scene or play the whole sequence with transitions before you render anything, then export it all to an MP4. Reopen a recent project any time to pick up exactly where you left off. Find it in Skales Studio under Scenes.
- **A real visual style for your images and animations.** Skales Visuals now has a style picker (premium dark, editorial, minimal, vibrant, retro, brutalist and more) that actually shapes the result, for both image and video.
- **Edit a visual right in chat.** After an image or video, a Refine line lets you ask for a change and the visual updates in place instead of starting over.
- **Send an image on WhatsApp and act on it.** An image you send Skales on WhatsApp is now saved to its workspace, not only described, so you can follow up on the go with things like edit this image or turn it into a video using your tools or a connected service.
- **Steer how images are read.** An optional extra instruction in the Vision Provider settings is added to every image description (for example read all on-screen text verbatim, or describe charts as data tables), which matters when your chat model has no vision and only sees the description.
- **Write very long files in parts.** A file too large to produce in one step can now be built up piece by piece, so large code files, full pages and long documents are written reliably instead of being cut short.

### Changed

- **Image and video each get their own direction.** Posters are composed as still graphics and animations as motion, and both now follow the colours, fonts and style you actually asked for instead of a fixed look.
- **Capable models use more of their output.** Models that can write more are no longer held to a small size, so longer answers and files come through in one go when they fit.
- **Visuals can use motion and 3D, not just flat effects.** Generated images and videos across Studio and chat can now reach for real animation and 3D depth, so results look closer to hand-crafted design instead of a flat template.

### Fixed

- **Big single-file builds finish instead of stalling.** When a task asked for one large file, it could be cut off partway and the run would get stuck retrying. It now completes the file and the task, across more models.
- **Attaching an image no longer shortens the reply.** A photo or screenshot in chat could cause the answer to be cut short; it now comes through in full.
- **Your Brand Kit logo shows up in visuals.** With Brand Kit on, your logo is placed in the generated visual instead of leaving a blank.
- **Image reading fails honestly.** When a configured Vision Provider cannot read an image, Skales now says so and points you at the settings, instead of quietly passing the error on as if it were the picture.
- **Planning no longer fails over how a model formats its answer.** Scene planning and other AI steps used to break when a model added a note or extra text around its answer; they now read the result cleanly across more models.

## v11.4.51 - Duet

### Added

- **Take over a team task as its own chat.** From a shared team plan you can open a task assigned to you as its own chat that carries the plan's context, while the shared plan stays in Teams. A task you take on becomes a real conversation in your History, and nothing is lost on either side.
- **Read PDF, Word and Excel files in chat.** Point Skales at a .pdf, .docx, or .xlsx and it reads the text out, so you can ask questions about a real document, not just plain-text files.

### Changed

- **Faster to start when you have a long history.** Skales no longer re-reads your entire chat history every time it launches, so opening it stays quick even after months of conversations.

### Fixed

- **ChatGPT subscription works for real work, not just replies.** Signed in with a ChatGPT subscription, Skales now uses it for tool use and agent tasks too, lets you pick the model from a list you can edit, and shows as connected once you are signed in.
- **Reading a past chat no longer reorders your History.** Opening a conversation just to read it keeps it where it was; only sending a new message moves it to the top.
- **Short answers in chat finish cleanly.** A brief reply no longer leaves a chat looking unfinished.

## v11.4.50 - Connect

### Added

- **Connect MCP servers that need a sign-in, not just an API key.** Remote MCP servers that authorize through your account (for example Higgsfield) now work: add the server, click Sign in, approve in your browser, and its tools come online. Pick Higgsfield from the quick-setup list to add it in one step. Skales keeps you signed in and renews access on its own, and Sign out is one click on the server row.

### Changed

- **A skill you edit takes effect right away.** When you change one of your own skills, the next time it runs it uses the updated version instead of the old one until a restart.
- **Steadier through an unexpected restart.** Skales records its background state more carefully, so an unexpected restart no longer resets your hourly usage limit or drops recent activity.

### Fixed

- **A long-running scheduled or background task no longer runs twice.** When a background task took longer than its time limit, the old run could keep going while a retry started alongside it, which risked repeating an action like sending the same message twice. The run is now stopped before the retry begins.
- **You are told when the Telegram bot gives up.** If the bot fails to start over and over and Skales stops retrying to avoid a loop, you now get a heads-up to reconnect it instead of it going quiet.

## v11.4.25 - Steady

### Fixed

- **Google Gemini works in chat again.** Chats on a Gemini model reply reliably, tools included. Image generation was unaffected.
- **The aspect ratio you choose is applied.** Picking 1:1, 9:16, or 4:5 produces an image in that shape, in Studio and in chat.
- **Chat stays reliable.** Resolved a case where chat could stop responding and now keeps going on its own.
- **First-time setup always runs on a new install.** The welcome flow is no longer skipped the first time you open Skales.

## v11.4.24 - Handshake

### Added

- **Generate images and video from the chat composer, now with fal.ai.** The generation toolbar (the Sparkles button above the message box) offers fal.ai alongside Skales Visuals, Google, and Replicate, for both images and video: Flux, Recraft, and Ideogram for images, LTX-2.3 for video. A custom-model field lets you point a provider at any model id, so a newer model works without waiting for an app update.

- **API Connector: connect any REST API from its own docs.** A new tab (Settings > API Connector) turns an API's documentation, or an OpenAPI spec, into a connector the assistant can call. Paste the docs or a docs URL, press Generate, review the endpoints, confirm the target domain, and add your key. The key is stored encrypted on your machine and is only ever sent to that API's own host, never to the model. Trigger a connector in chat with #name, for example #sevdesk. A read runs right away; anything that writes asks you to confirm first. Chat-style LLM APIs are detected and pointed to the AI Provider settings instead.

### Fixed

- **Asking Skales to generate an image works again in chat, WhatsApp, and Telegram.** When a Google API key was set but its image quota was empty, the request failed instead of trying another provider. Image generation now falls back across the configured providers, so a plain request like "generate an image of ..." produces an image whenever any image provider (Google, Replicate, or fal.ai) is set up.

- **Team pairing now connects both computers, not just one.** When you entered the code on the second computer and pressed Connect, it stayed on "connecting" while the first computer opened the chat. The computer that enters the code now confirms automatically, since typing the code and pressing Connect is already the intent, while the computer that shows the code still confirms the incoming peer once. Both sides land in the conversation. The two also exchange their hello reliably no matter which joined first, so connecting no longer depends on timing.
- **The pairing confirmation is no longer hidden behind the code window.** On the joining computer, any confirmation now appears inside the pairing window instead of behind it.

## v11.4.23 - Follow Through

### Added

- **Paste an image into the starting chat box.** The opening composer now accepts a pasted image the same way the in-chat composer already does. Uploading and dragging an image in already worked there; pasting now does too.

### Changed

- **The tuning profiles for reasoning models tell them to act in the same turn.** The built-in profiles for Kimi, DeepSeek, GLM, MiniMax, Qwen and Skales IQ now carry an explicit line: when the model says it is about to use a tool, make the call in that same message instead of posting a "let me..." line and stopping. This reinforces the first-reply fix below at the model-tuning layer, and the same update goes out to the community profile library so it reaches imported profiles too.
- **Profiled models share a common voice across every channel.** On top of the per-model tuning, the profiles now tell the model to answer like a colleague: no filler or thanks-for-reaching-out, own a mistake in one line instead of apologizing in a loop, never claim it has no real-time data (use web search), and check the context before asking a question. These correct common habits of weaker models and apply wherever a profile is active, which is chat, Friend Mode, Telegram, WhatsApp, the Buddy and now Autopilot's reports and plans too. Strong frontier models match no profile and are unchanged.

### Fixed

- **The assistant acts on its first reply instead of stalling.** Some models would open by saying what they were about to do, like "let me pull up your notes", without actually calling the tool, and the turn ended there, so you had to tell it to continue. It now follows through and runs the tool in the same turn.
- **The Vision Provider's per-surface switches take effect.** The Chat, Telegram and WhatsApp switches under Settings, Vision Provider were not being read, so a configured Vision Provider read every image on every surface regardless. They now work. They stay on by default, so an existing setup keeps reading images exactly as before; turning one off sends images on that surface straight to your model instead of through the Vision Provider, which is what a vision-capable model wants.

## v11.4.22 - Real Work

### Changed

- Scheduled tasks, planner tasks, and autonomous tasks now run as deep as a goal instead of stopping after a handful of steps. They follow your Goal step budget (default 80, or 0 to run to completion) and the per-task time limit, so a scheduled job that does real multi-step work actually finishes rather than reporting done half-way.
- Multi-agent subtasks run deeper so each agent completes its part.
- Tasks sent from Telegram, WhatsApp, the desktop Buddy, and the CLI get a real working budget: about 40 steps on a normal model and up to 80 on a strong cloud model. The Buddy now scales with the model like the channels do.
- The WordPress and in-page Browser agents run more steps for multi-page work, and the per-turn tool-call ceiling is higher so a run that touches many files at once is not cut short.
- Raised the autonomous hourly call budget so the deeper task runs are actually reachable.

## v11.4.21 - Overall Improvement

### Added

- **LLM Profiles keep themselves current.** The per-model tuning library now refreshes from the community repo on its own, about twice a day, so a newly released model is tuned the moment a profile is published, with no Skales update to wait for. Your own imported profiles are never touched, and it does nothing if you have LLM Profiles switched off.

### Changed

- **Google (Gemini) requests send your API key as a header.** The key now travels in the standard `x-goog-api-key` header instead of inside the request URL, across chat, image and video generation, model listing, vision and everything else that talks to Google. It keeps your key out of any URL logs and lines up with Google's current key handling. Subscription-token (BYO) sign-ins are unchanged.
- **Studio leads with current image and video models.** The video picker now offers Veo 3.1, Veo 3.1 Fast and Veo 3, plus Kling 2.0, 1.6 and 1.5. The image side adds Nano Banana Pro and Nano Banana 2 alongside Imagen 4 and Gemini Flash Image.
- **GLM models are tuned out of the box.** GLM now ships with the same guidance the other strong models get - reach for the right tool instead of giving up, and on a long task keep the checklist current and continue rather than restart - and its context and output limits match the current GLM generation instead of the older, smaller caps.
- **Playground builds richer apps and says what it is.** Generated mini-apps are now told about the helpers they already have (live web search, saved data, what Skales knows about you, notifications, clipboard) so they build on those instead of asking for keys; the suggestions stick to things that can actually be built; the screen now says it builds mini-apps; and it has a clearer icon. Quality Boost uses the current Claude Sonnet.
- **Your Wrapped badge reflects a big week.** A heavy week now earns Power User, Researcher or the new Unstoppable instead of the generic "Balanced", and the weekly activity count is no longer capped low, so a real high performer sees their real number.
- **Goals run much further before pausing, and you decide how far.** The default step budget is higher, so a long task is interrupted far less often. Settings > Goals now has a Goal step budget you can raise, or set to 0 to let a goal run to completion, checking in only at a high safety limit, which suits strong cloud models. A goal runs on its own in the background, so it can finish while you are away. When a goal splits into several independent strands, the agent can also fan them out as parallel sub-agents instead of doing each one in sequence.

### Fixed

- **A long task picks up where it left off.** When a long task pauses for you to continue, pressing Continue now carries on with everything it had already done instead of starting over, and the Continue / Auto / Stop choice appears on its own instead of only after you reload the window.
- **Turning on Codework or Swarm lands in the right place.** The enable button on those screens now opens Add-Ons, where the switch lives, instead of the general Settings page. The Codework description also explains it now lives in chat via `/codework`.
- **Playground's "what I know about you" lookup works.** It returned nothing before; it now answers from your profile.

## v11.4.20 - Skales IQ

### Added

- **Skales IQ, a free trial built in.** You can now start chatting with a capable AI model right away, with no API key of your own. Skales IQ runs on our servers, includes tool use and vision, and you can turn it on from the first-run setup or in Settings under AI Providers. When the free trial runs out you can add your own key and keep going for free, or switch to a local model.
- **Skales Stack.** A new toggle that prefers fast, deterministic local execution for tasks Skales can do without calling a model, which saves tokens and is quicker and more reliable. It shows you which local capabilities, like media, browser, and search, are ready on your machine.
- **Replies stream in more promptly.** The chat now reacts the moment the assistant produces text, so the first words and each update appear without the slight lag of the old fixed polling cadence. If the live connection is ever unavailable it falls back to the previous polling, so nothing breaks.
- **Turn a plan into action with one tap.** In Plan mode the plan strip now has a Build this plan button. It switches to Code mode and starts carrying out the plan in your bound folder, so you review the plan and press go instead of switching mode and retyping the request. You still approve the changes (or allow edits for the session), and you can pick Auto yourself for hands-off.
- **Undo any file change in Code mode.** When Skales writes, edits or deletes a file in a folder-bound chat, the change now carries a one-click Undo that restores the file exactly as it was, right from the conversation. It is saved per change before the edit happens, so you can let the assistant work and roll back anything you do not like. Undo covers file changes only; a sent message, a deploy or a git push still asks first, which is why those keep their confirmation.
- **Undo a whole turn at once.** When the assistant changed several files in one go, an Undo all changes button reverts the lot in a single tap, on top of the per-change Undo.
- **See how big a change was at a glance.** A file edit or write in Code mode now shows a small added/removed count next to it, so you can tell a one-line tweak from a rewrite without opening the diff.
- **Point Skales at a file with @.** In Code, Plan or Auto mode, typing @ now suggests the files in your bound folder, right next to your skills, so you can reference the exact file without typing the whole path.
- **Plan mode asks before it plans.** When a request leaves something open, Skales now asks one to three quick clarifying questions you answer with a tap, before it lays out the plan, so it builds the thing you actually meant.
- **Bring a file's contents in with @.** Building on the @ file picker, mentioning a file from your bound folder now sends its actual contents along with your message, so the assistant reads the file directly instead of spending a step opening it. It works in Code, Plan and Auto mode, is capped per file, and only the files you name are included.
- **The @ menu is sorted into sections.** Typing @ now groups what it offers into Skills, MCP, Workflows and Files, each under its own heading, so a longer list stays easy to scan. Workflows you have created are selectable here and drop in their /goal- command.

### Changed

- **AI Providers now leads with Skales IQ.** The provider list and the first-run setup show Skales IQ first, so getting started no longer means hunting for an API key.
- **Friend Mode starts off on a new install.** Companion check-ins are now something you switch on yourself under Notifications, so a fresh install stays quiet until you ask for it. If you already had Friend Mode on, nothing changes.
- **Discover feed alerts are off until you turn them on.** Being mentioned by someone in the Discover feed, or a post trending there, no longer notifies you out of the box. Enable Discover mentions or trending under Notifications if you want them.
- **Shorter welcome message.** The first-run greeting is trimmed and less boastful.
- **In Code mode, big projects lead with the files that matter.** The project map Skales gives the assistant for a folder-bound chat now keeps the most relevant files when it has to trim for space, so on a large codebase it finds the right file more reliably.
- **Your free Skales IQ balance goes entirely into real conversations.** The trial now spends its balance on the chats you actually have, so the free allowance lasts longer.
- **File edits apply more reliably.** When the assistant changes part of a file, a small difference in spacing or indentation no longer causes the edit to fail and waste the step; it still lands as intended. A precise model is unaffected.
- **Workflow is available out of the box.** The Workflow builder, the hand-drawn half of the GOAL system, now ships switched on for new installs, so it is in the sidebar from day one. If you had it turned off, your choice is kept; you can still toggle it under Add-Ons.
- **Codework now lives in the chat.** Codework is no longer a sidebar item or a template module. Everything it did is in the chat's Code mode (Chat, Code, Plan or Auto on a bound folder), and Codework itself stays reachable any time with the /codework command. Existing users keep full access; there is simply one coding home now instead of two to keep in step.
- **The Ollama model marketplace starts collapsed.** On the AI Providers screen the long model marketplace is now a section you expand when you want it, so the page stays scannable, and a couple of lighter Gemma 3 sizes are listed for smaller machines.
- **The sidebar reads cleaner in non-Latin languages.** A couple of plain menu words that were still in English for Russian, Chinese, Japanese and Korean (Business, and Templates in Russian) now show in the local script. Product names like Lio AI, Swarm, Discover, Spaces and Studio stay as they are, the same way an English app keeps them, so nothing reads as a clumsy literal translation.

### Fixed

- **Switching back to Skales IQ is one click.** If you move to your own provider and later return to Skales IQ, it picks your existing trial back up instead of asking for your email again.
- **A nearly used-up Skales IQ trial now warns you in the chat**, not only on the Settings screen, so you are not surprised mid-task.
- **Notifications no longer pile up.** Identical alerts arriving in quick succession are collapsed into one, so a chatty feed can never flood the notifications list or stack a wall of toasts.
- **Toasts sit in the top-right corner again.** They could drift into the middle of the window and shift around when you clicked elsewhere; they now stay pinned where they belong.
- **Scheduled tasks and reminders fire on their own again.** A schedule ran when you pressed Run by hand but would not always go off by itself, even with Autopilot on. It now fires reliably at the set time on all systems, including Windows. The macOS case where the app had been put to sleep in the background, and reminders saved with an unusual time format, are handled too.
- **A reminder shows up the moment it fires.** A scheduled reminder or task result now appears live in the open chat, and as a notification and toast, instead of only after you reload the conversation.
- **The chat could fail to reply on an older profile.** If your saved profile came from an earlier Skales version and was missing some fields, the assistant could hit an error while assembling its context and simply not answer. Skales now fills any missing profile fields from the defaults as it loads, and re-completes them after its background identity upkeep runs, so an older or partially written profile no longer breaks a reply.
- **Skales Stack handles grouped numbers correctly.** A sum written with thousands separators ("1,000 + 1" or "1.000.000 + 1", and the same in percentages) could be answered wrong because the separator was misread. Those are now handled correctly, and plain numbers and ordinary decimals stay instant.
- **A used-up Skales IQ trial gives a clear next step, not a confusing error.** When the free trial runs out while it is your active provider, you now see the plain "trial used up" message and a button to choose another provider, instead of the chat failing with an unrelated local-model error. A trial that was ended on the server is recognized right away rather than retried, and an expired trial can no longer be re-selected by mistake.
- **Skales Stack stays out of the way while you are coding.** In a folder-bound Code, Plan or Auto chat, a quick "what time is it" or "5+5" no longer short-circuits past your code session; the folder, repo view and your code model stay in charge.
- **The Skales IQ consent line reads correctly in every language.** The data-notice sentence shown when you start the trial was breaking mid-sentence in German, Japanese, Korean, Turkish and Vietnamese. It now reads as one proper sentence in all twelve languages, and the trial skip notice, the email field label and the Skales Stack capability chips are translated too.
- **A skipped trial task no longer looks like a failure.** A scheduled task that is not run on the free trial now shows as a neutral skip on the Schedule page instead of a red error, and no longer sends a false "task failed" alert each time it recurs.
- **Two different reminders that happen to read alike both arrive.** The duplicate-collapsing that keeps a chatty feed from flooding your notifications was a little too eager and could merge two genuinely separate reminders or task results into one. Per-event notifications now always come through; only true repeats of the same alert are still collapsed.
- **A stuck step no longer becomes a goal.** If the assistant gets stuck repeating the same step on a simple request, it now stops with a short honest note instead of quietly parking the rest as a long-running goal. Goal hand-off still happens for genuinely long tasks.
- **A file change shows its diff and Undo without a click.** A write or edit in Code mode now puts the colored before-and-after, the added/removed count and the Undo button right in the conversation, instead of tucking them inside the collapsed activity panel, so you see what changed at a glance.
- **An approval card from another chat no longer lingers.** After a reload, a pending approve-and-run prompt that belonged to a different conversation could briefly appear in the chat you were looking at. The chat now drops any approval that does not belong to the conversation in view.
- **The Skales IQ brand mark sits still.** The faint gecko watermark on the Skales IQ panels, in first-run setup and in Settings, was shifting as the panel grew taller; it is now pinned to the top corner and a touch smaller, so it stays put.

### Easter eggs

Since a few of you have written in asking, here is the full list of the hidden bits in Skales. You can also just ask Skales which easter eggs it has and it will list them.

- **`/coffee`** - a tongue-in-cheek "still brewing" reply.
- **`/servus`** - a Viennese hello back.
- **`/wien`** (or **`/vienna`**) - a greeting from Vienna with an ASCII Stephansdom.
- **`/sachertorte`** - the authentic Vienna Sachertorte recipe card.
- **`/nudge`** - shakes the Desktop Buddy window, MSN Messenger style (or press Cmd/Ctrl + Shift + N).
- **`/barrelroll`** (or **`/do a barrel roll`**) - spins the chat a full 360 degrees.
- **`/highfive`** and **`/bow`** - an animated 🙌 and 🙏.
- **`:gecko:`** 🦎, **`:bubbles:`** 🫧, **`:paw:`** 🐾 - inline animated emoji.
- **The Konami code** (up up down down left right left right B A) - unlocks a gecko surprise.
- **Click the Skales logo seven times** - collect the seven secrets; all seven earns "Master of Geckos".
- **Shake the Buddy window three times fast** - it asks you to stop shaking the gecko.
- **Start a fresh chat with "hello skales"** (or hi / hey skales) - a personal greeting just for you.

## v11.4.10 - Run ▶️

> **Scheduled reminders and tasks actually run and arrive, on any model. Integrations that were only promised now actually work. Autopilot can be paused again, code in chat stops turning into math, and your scheduler state is safe from silent corruption.**

### Fixed

- **Integrations that were only promised now actually work.** A regression from the v10 settings and sidebar reorganization: a number of integrations got a place in Settings and were announced to the assistant as things it could do, but their tools were never wired up, so the assistant said "that tool is not available" the moment you asked. Google Docs, Signal, Slack, Discord, the network scan and email reply are now connected end to end, so once you set the integration up the assistant can actually use it. Webhooks, which is an inbound trigger with nothing for the assistant to call, no longer pretends to have a tool.

- **The assistant stops claiming an integration it does not have.** Every integration was announced to the assistant the moment it was switched on, even before you connected the account, so it would confidently offer to post to Slack or read a Google Doc and then fail. It now goes by live status: a tool is only offered once the integration is actually configured, and the capability list marks each one connected or needs-setup, so the assistant points you to finish setup instead of failing silently.

- **Scheduled reminders and tasks actually run, on any model.** A reminder you set ("remind me at 20:00") could be skipped before it ever ran, both when it came due and when you pressed Run by hand, with nothing delivered. Before running a task Skales asked a model to rate its own confidence against a fixed tool list that did not even include the reminder and messaging tools, so an honest model scored a deliverable reminder low and the task was dropped at "0/100". Skales now runs the task directly and lets it report honestly if it truly cannot finish, so your reminders and scheduled jobs come through whatever model you run, including a small or free one.

- **Pausing Autopilot actually pauses it, and a task you start runs.** The pause button followed the background runner, which Friend Mode and the Always-On agent keep alive, so it sprang back to on and would not switch off, while the task queue it controls was already off. A task you added then sat there doing nothing while the page still looked like Autopilot was running. The switch now follows your real Autopilot setting, so off means off and on means your tasks run, and a warning tells you when you add a task while it is off. Friend Mode is untouched.

- **Code and shell commands in chat render as text, not math.** A message containing a dollar sign, a PowerShell $variable, a price, a snippet, could be swallowed and shown as italic mathematics. Single-dollar math is now off, so those read as plain text; genuine block math still renders.

- **Video and Type exports finish cleanly and keep their download.** An export could finalize with an empty download link and skip saving to your gallery, with only a toast to show for it, because the job was flipped to done a moment before its file was attached. An export now completes only once its file is really there, and the Download button stays until your next render instead of disappearing after thirty seconds while you still needed it.

- **Your scheduler and settings files cannot be silently corrupted.** Planner tasks, cron jobs and settings were written in a way that a crash or a power cut mid-write could leave half-written, and a damaged file was then quietly read as empty, so scheduled tasks, jobs or even settings could vanish with no sign while the file still sat on disk. Writes are now all-or-nothing, and a file that cannot be read is set aside as a copy and logged instead of being silently dropped.

- **Your phone and paired devices stop dropping every few minutes.** The relay that links your phone, your paired computers and Teams was closing healthy connections after five minutes, so the mobile app, QR pairing and Teams sessions fell into a constant reconnect loop. A connection is now kept alive for as long as the device is really there, and only genuinely dead ones are cleaned up, so the link stays stable through long sessions and slow mobile networks.

- **A task you send over WhatsApp, Telegram or the Buddy no longer chokes on a big result.** A long file or a long page read through a tool used to be handed back whole and could overflow a smaller model partway through, so the job died with a provider error. It is now sized to the model the same way the desktop chat already does, with anything genuinely large saved to a file the assistant can page through, so the task runs to the end.

- **A weaker model stops burning a whole channel task on one repeated step.** The loop protection the desktop chat has, which notices a step that already ran with the same input and moves on instead of repeating it, now runs on WhatsApp, Telegram and the command line too. A model that used to circle on the same call until the budget ran out now gets more done.

- **A capable model gets more room to finish a multi-step job from your phone in one go.** A task sent over Telegram or WhatsApp keeps a safe step limit on a small or local model, but a strong model is given a larger budget, so a real piece of work is far less likely to stop halfway. When it does need more than one message, the reply you send simply continues it.

- **Scheduled reminders and tasks reach the background scheduler.** The 60-second pulse that runs reminders, scheduled tasks, Friend Mode and identity upkeep called the local server on "localhost", but the server listens on IPv4 only, and on current runtimes "localhost" can resolve to the IPv6 address first (common on Windows). When it did, the pulse never reached the server and nothing scheduled ever ran, while the app looked completely normal. The pulse now uses the IPv4 address directly, so a reminder set for 13:11 fires at 13:11. This was the cause behind reminders that were created but never arrived.

- **A reminder with an odd time still fires.** A reminder whose time came back in a form other than 24-hour HH:MM (for example "5:56 PM", "17.56", "1756"), which a smaller model can produce, was stored as-is and then read as never due, so it sat enabled and silently never ran. The time is now normalized when the reminder is created, and a reminder that somehow still has an unreadable time is logged instead of failing in silence.

- **Reminders also survive App Nap on Mac.** On macOS the system could suspend the background pulse once the window lost focus. Skales now keeps the scheduler alive while it runs, so a reminder fires whether or not Skales is the front window.

- **Line breaks in Type are kept.** In Studio's Type, pressing Enter now starts a real new line in both the live preview and the exported video, instead of being flattened into a space, so a two or three line headline lays out the way you typed it.

- **Discover spaces show their full history, not just the last few hours.** A space like Skills could look empty even when people had shared plenty, because it only ever scanned the most recent slice of the feed and busy chatter pushed the older posts out of view. Each space now pulls its whole history from the server, newest first and paged, so shared skills, studio work and the rest stay findable for as long as they live in the feed.

- **What you share from Studio actually turns up in the Studio space.** A shared image or Skales Visual landed in the feed but could not be reached through the Studio tab, so it felt like it had vanished. Shared Visuals are now filed under the right space and surface there alongside your images.

- **Sharing from Studio no longer posts your prompt as the caption.** Sending an image or a Visual to Discover used to attach the generation prompt as the post text, which read as noise. It now uses a neutral caption, and your name is already on the post.

- **A poll in Discover shows once.** A shared poll used to render both the structured poll and a duplicate of its raw question and options as plain text. The poll now shows on its own, and a post with no text no longer leaves an empty line.

### Added

- **See your own Discover posts in one place.** A My Posts view in Discover gathers everything you have shared, including anything still waiting on review, so you can find your own work without scrolling the whole feed.

- **Reset the scheduler when it gets stuck.** A new button in the Autopilot Control Room backs up and rebuilds the scheduler, planner and autopilot state in one step, clears any stuck run and restarts the background runner. Your settings, keys, Friend Mode, chats and memory are kept, and a backup is saved first, so it is a safe way out when scheduling has tangled itself up.

- **Skales now learns from WhatsApp, the Desktop Buddy and the desktop chat, not only Telegram.** What matters to you, your projects, your wording, the things you ask it to remember, is now built up from the surfaces you actually use rather than a single one, so it becomes a better companion wherever you talk to it. On WhatsApp only your own conversation feeds your memory; a contact you let through never writes to it.

- **Friend Mode can reach you in the Desktop Buddy bubble.** A new option under Notifications, Friend Mode, lets a proactive check-in appear in the Buddy bubble alongside your main channel. It stays quiet while you are already chatting on the desktop, and it is off until you switch it on.

- **Choose how Friend Mode sounds: Friendly or Business.** A new toggle in the Friend Mode box keeps the warm companion voice by default, or switches proactive check-ins to a neutral, concise note for when you want the nudge without the small talk. Only the voice changes; what it knows about you and the topics you asked it to drop stay exactly as they were.

- **In Code mode you see a real before and after for every change.** When the assistant edits or writes a file in a folder-bound chat, the change now shows as a green and red diff right in the conversation, so you can read what moved at a glance instead of just a note that a file was touched.

## v11.4.0 - Animate:Type 🎬

> **Turn a line of text into an animated, looping video right inside Studio, plus more reliable tool execution on any model and a set of long-standing rough edges gone.**

### Execution

- **Tool results stop getting cut off.** When Skales read something through a connected tool (a GitHub issue, a Notion page, a long file), the result was trimmed to a tiny slice before the model ever saw it, so the model worked from half the picture and sometimes filled the gap by guessing. Results now arrive in full, sized to the model's context, with anything genuinely huge saved to a file the model can page through, so nothing is silently dropped. You will feel this most when reading issues, pages and large files through tools and MCP servers.

- **Files actually get written, even from a weaker model.** A model writing a large file (a long HTML page, a report) could trip over its own formatting and the whole write was thrown away, so a task looked busy but produced nothing. Skales now recovers the file content in that case and writes it, instead of dropping the action. Models that already format correctly are unchanged.

- **Weaker and local models call tools more reliably.** On a turn where Skales offers tools, a per-model profile now samples more steadily, so a model emits well-formed tool calls instead of malformed ones, and it is reminded never to claim it did something (wrote a file, sent a message) unless a tool actually confirmed it. This is the difference between a model that circles and apologizes and one that gets the job done. Frontier models are untouched.

- **LLM Profiles cover today's models.** New built-in profiles for Mistral's Devstral coding model and NVIDIA's Nemotron, which previously matched no profile and ran untuned, plus an optional per-model setting for how a model samples specifically while calling tools. Profiles never remove a tool or a capability; they only help a model use what it already has.

### Fixed

- **The "Fallback active" banner only shows when fallback is actually on.** After a one-time provider failover, the orange banner could stay up long after you turned provider fallback off, even pointing at a provider you were not using. It now clears the moment fallback is disabled and never appears while fallback is off; a real failover still shows it.

- **Your chat history shows the date, not just the time.** Each conversation in History now shows the day and the time in your language, so a chat from last week reads as last week. Every conversation also has a download button to save its full transcript as a Markdown file, from the desktop app or a remote browser.

- **Skales Visuals stop coming out blank.** A visual built from a rich prompt could screenshot before its fonts and content had painted, landing a blank white image in your gallery as if it had worked. Skales now waits for the page to actually render, checks the result is not blank (and retries, then tells you instead of saving an empty image), and no longer pushes photo-camera styling into a design that is really HTML, so your colors, fonts and layout come through.

- **Discover suggestions and the background Briefing refresh are steadier.** A malformed leftover entry can no longer sit in the suggestion queue, and both the suggestion generator and the every-3-hours Briefing refresh now record clearly when and why they ran, so a quiet feed is diagnosable instead of silent.

- **Display fonts in Type and Studio always load.** A font that does not publish the exact weight you picked no longer drops silently to a plain system font in the live preview and the exported video. Skales now asks the font service for a weight the font really has, so the typeface you chose is the one you get.

- **Video export ends cleanly, and a transparent clip says so.** A failed encode now reports a clear error instead of being able to disturb other background work, and when you pick the transparent background the buttons and the gallery label the result a WebM (alpha), which is what it is. A transparent export your machine cannot encode now stops with that clear error instead of hanging.

- **Your connected MCP tools show up in chat.** With MCP servers connected and running, the assistant now sees and calls their tools directly, and when you ask what it can do it lists your actual servers and their tools by name instead of a generic placeholder. It no longer tells you it has no MCP tools when the tools are right there.

- **A colorful Skales Visual is no longer mistaken for blank.** A design whose background is a gradient or image set in its stylesheet is kept, instead of being rejected as an empty render.

- **A designed PDF does not stall on a slow font.** Rendering a PDF from HTML waits only briefly for a web font, then prints with what is ready, so a slow font host can no longer hang the export.

### Added

- **Choose how often each kind of notification reaches you.** On the Notifications page, each group (Tasks and Planner, Calendar, Messages and Email, Companion, Discover) now has a frequency: Instant, Often, Once a day, or every 12 hours. Throttling only affects live pings (toasts and channels); every event is still recorded on the Notifications page, and important ones (an approval you need to give, a contact writing you) always come through. Everything starts on Instant, so nothing changes until you turn it down.

- **Type: turn text into an animated video, free.** A new Studio tab (Type) makes a looping animated video from a line of text, with no AI and no setup. The headline is a set of fourteen Motion presets driven by a real timeline engine, with custom easing, per-letter staggers and depth (Cascade, Drop, Pop, Bounce, Slide, Reveal, Flip 3D, Spin, Wave, Breathe, Glitch, Neon, Shine, Typewriter); pick one and it just works, no knobs to set. Eighteen simpler presets sit below them. Choose a font, weight, colors, background and length, set the aspect, and keep Loop on for a clip that repeats without a jump. Watch the live preview, then export an MP4 through the same renderer Skales already uses for video. Pick the transparent background and it exports an alpha WebM you can drop on top of other footage.

- **Designed PDFs come out clean.** Skales now renders a designed PDF straight from HTML as a proper A4 document, with real page breaks and backgrounds and colors intact, and no browser print header or footer pasting file paths and timestamps across the top and bottom. So a brochure, a product sheet or a branded report looks the way it should, instead of an improvised browser print. Plain editable text still goes through the document writer and data tables through the spreadsheet writer.

### Changed

- **The advanced provider settings sit together at the bottom.** Per-Mode Model Overrides, Per-provider Timeout, Retries and Override Model Limits are now grouped at the end of the AI Provider tab instead of being scattered through it. Your saved values are untouched. A short note under Advisor Strategy now also explains how Advisor, your chat model choice and provider fallback relate, so it is clear which one wins.

- **Notification frequency reads from most to least.** Each category's frequency now runs Instant, Often, every 12 hours, then Once a day, so a step to the right always means fewer live pings.

- **The custom personality box explains itself, in every language.** The Soul / System Identity field now says plainly that it is your own system prompt and overrides the personality preset, and that Skales' long-term identity and memory are kept separately. Its description is now translated instead of English-only.

## v11.3.6 - Schedule Recovery ⏰

> **Missed reminders come back, and schedules stop failing in silence.**

- **A missed reminder catches up instead of vanishing.** A one-time reminder set for a moment the app was closed used to be lost for good - it kept showing as Active but never arrived. It now comes through the next time you open Skales, marked as overdue, and a reminder too old to still make sense is cleared away instead of sitting there forever.

- **Recurring reminders no longer need the app open at the exact minute.** A daily, weekly or monthly reminder now arrives on the first check at or after its time, so a closed lid or a busy moment right at the scheduled minute no longer skips the whole day.

- **The Schedule log tells you when nothing ran.** If a scheduled task is held back because the Always-On Agent and Autonomous Mode are both off, the log now says so, instead of sitting empty and looking broken.

- **A stuck approval no longer freezes the chat.** If the app restarted while an action was waiting for your confirmation, the approval card could hang with dead Approve, Cancel and Stop buttons, and kept waiting even after you left and reopened the chat. It now clears itself on the next start, and the buttons work again on a reopened card.

- **Scheduled and recurring tasks report back in the app.** A task that runs in the background - a reminder, a recurring job, the daily stand-up - now delivers its full result into chat as a message you can read and reply to: in the conversation that asked for it, or in a dedicated Tasks chat. So even with no WhatsApp, Telegram or email connected, the report still reaches you. You can turn it off under Notifications.

- **Some models stop spilling their actions as text.** On certain connections a model would print the inner workings of an action into the chat instead of carrying it out. Skales now understands that, so the action runs and the clutter never reaches you.

- **A schedule it cannot read is caught right away.** Asking for a repeating task with a timing Skales cannot make sense of is now turned down with a clear example, instead of quietly creating a schedule that could never run.

- **Strong models keep all their tools, small ones keep the right ones.** A capable model (DeepSeek V4 and the like) was being treated as a tiny one and cut down to a handful of tools, so it could not even create a reminder - that is fixed, it now has the full set. A genuinely small model still keeps the essentials (files, web, email, calendar, reminders, schedule), and a model unsure which tool fits now looks it up instead of guessing. "remind me at 20:50" reliably becomes a reminder again, not a failed calendar entry.

- **A model that reaches for the wrong tool name still gets it right.** When a model wrote out a tool call under a name borrowed from another tool (create_file, str_replace, bash) or with a small typo (write_files), Skales used to quietly drop it, so it looked like nothing happened. It now recognizes the intended tool and runs it, so smaller and local models get more done on the first try. Models that already name tools correctly are unaffected.

- **The agent stops re-running the same step dressed up differently.** A repeated tool call that differed only in how a path was written ("src/index.ts" versus "src/./index.ts") or in the order of its fields used to slip past the loop guard and burn steps for nothing. Those now count as the same call, so a weaker model wastes far fewer steps going in circles.

- **Long working chats no longer run out of room mid-task.** A long back-and-forth that uses tools, especially on a small local model, could fill the context window partway through and stop with a provider error. Skales now shortens the conversation on its own when it gets tight during a task, not only at the start, so the work keeps going.

- **Code changes show up as a real diff, not a wall of text.** When the agent shows you a git diff in chat, it now renders the way you expect: green added lines, red removed lines, so you can read a change at a glance instead of squinting at plain text. A very long diff is trimmed with a note to open the file for the rest.

- **Code mode sees the whole project, not just the open folder.** When a chat is pointed at a folder (Code, Plan or Auto), Skales now builds it a map of the project, which files exist and what each one exports and imports, and hands that to the model, so it heads straight for the right file instead of reading its way around a larger codebase. The map is built once and reused, and a small project skips it since the plain file list is already enough.

- **Pick Code, Plan or Auto right from the start screen.** The Chat/Code/Plan/Auto switch is on the New Chat screen now, not only inside an open chat, so you can point a chat at a folder and choose how it works before you type the first message. A folder outside the allowed area asks the same way it does inside a chat (add this one folder, or full disk access), and your choice carries over so the very first message already works in that folder.

- **The mode bar under the composer no longer pops in.** The context readout and the Chat/Code/Plan/Auto strip used to flash in a moment late on a fresh chat, around the time the first reply started. They are there from the first paint now.

- **The coding agent stays anchored to your folder.** On a long coding task a weaker or hotter model could drift: talk about a different folder than the one you bound, or apologize in circles instead of just re-running the check. Skales now keeps every model grounded in the bound folder and the live working state, and asks it to confirm a path or a file's contents with a tool before stating them, so it acts on what is really there instead of guessing.

## v11.3.5 - Hide & Seek 🔍

> **The background comes back to life.**

- **Scheduled tasks, goals, reminders and proactive messages run reliably again.** On some setups the background work could quietly stop while the app looked completely normal. It now runs dependably, every launch.

- **Friend Mode reaches you on WhatsApp again.** Check-ins arrive on schedule, and WhatsApp is ready in the background so they actually get delivered.

- **One stuck job no longer holds up the rest.** A single long-running task can no longer block everything else waiting in the queue.

- **A Multitask job no longer leaves the chat stuck.** If a multi-agent job got interrupted, the chat used to keep showing it as running with no result. It now recovers cleanly and shows the job as interrupted.

- **Skales stays Skales on every model and in every mode.** In some situations - Deep reasoning, Code mode, a running goal, the document panel, or a custom Agent - the assistant could lose its identity and even claim to be a different AI. It now keeps its identity, its tools and its safety rules in every mode.

- **Identity Maintenance is set up for you.** The nightly memory-and-identity routine is ready out of the box (you can still turn it off).

## v11.3.4 - Notifications 🔔

> **One clear home for notifications, and they explain themselves.**

- **One place to manage notification types.** Notification settings used to appear in two spots. There is now a single place for them, on the Notifications page, with a link from Settings.

- **Every notification type explains itself.** Each switch has a short description of what it is, grouped by topic: Tasks & Planner, Calendar, Messages & Email, Companion and Discover.

- **Discover has its own switches.** Mentions and trending posts can now be turned on or off on their own.

- **No more repeated rows in your inbox.** Recurring updates no longer pile up as duplicates: one entry per event.

- **Notification titles read properly in your language.** Titles always show a clean, readable name.

- **WhatsApp stays linked across updates.** After an update WhatsApp reconnects on its own, so you no longer have to restart it and re-scan the code every time. One last scan after this update, then it holds.

- **Cloud API keys save reliably.** Adding and activating a provider now saves its key right away, so it is there when you open Chat, with no extra step.

- **Desktop buddy light mode, completed.** The screen-share button and the window picker now look right in light mode.

## v11.3.3 - Skales.app 🌐

A stability release. Less magic talk, more things that just work: reminders land at the right time, long tasks finish instead of starting over, and proactive messages actually reach you.

### Fixed

- **"Remind me in an hour" lands in an hour.** Relative reminders could end up at the wrong time. Skales now reads the real clock for them instead of doing the maths in its head, so "in 30 minutes", "in two hours", "tonight" all hit the moment you meant.

- **Long tasks finish instead of starting over.** On a bigger job the agent could re-write its checklist again and again, re-figure out where it was working, and burn through its budget without delivering. It now keeps one plan, ticks items off, remembers the original request and where it works, and carries that through to the end, even on smaller and local models. You will feel this most on multi-step work.

- **The progress card tells the truth.** The "X of Y done" on a running task sometimes showed 0 while the visible checklist was clearly further along. The card now reads the same checklist you see.

- **Files with many lines actually save.** A document or report the agent wrote could silently fail to be written on some models, so the task looked busy but produced nothing. Fixed - the file gets written.

- **Proactive messages reach you again.** Friend Mode check-ins and buddy nudges could go quiet entirely. They are back, and if the channel you picked is unreachable (for example WhatsApp needs a fresh QR scan), you now still get a notification instead of silence.

- **Reply to a forwarded message and it goes to the right person.** When Skales relays a message from one of your contacts and you answer with a short "you're right" or "tell her yes", it now understands your reply belongs to that contact and sends it to them, instead of getting confused.

## v11.3.2 - Re-Buddy

Your buddy does more than ever. The Desktop Buddy is Skales' most-used feature, and this release hands it everything the rest of Skales learned in the meantime - and that is just the beginning.

### Re-Buddy

- **The buddy finishes real tasks now.** It used to take exactly one step: decide, run a tool once, summarise. Now it runs the full agent loop - with the same generous working budget the channels get - calling tools, reading results and continuing until the job is done. "Clean up my downloads folder" actually cleans up your downloads folder.

- **Approve and it keeps going.** Approving an action from the speech bubble used to end the conversation with a one-line summary. Now the buddy executes what you approved and continues the task, asking again if the next step needs another approval. Declining is recorded honestly, so it never claims it did something you blocked.

- **The buddy has its own conversation.** Buddy turns used to be written into whatever chat session happened to be active, polluting your work conversations. The buddy keeps its own thread now, and "Open Chat" jumps straight to it - with the full transcript of everything it did.

- **It knows you.** The buddy speaks with the personality you configured under Settings > Identity, answers in your language instead of defaulting to English, and recalls relevant memories about you before answering.

- **Watch the buddy work, step by step.** While a multi-step task runs, the speech bubble now shows live progress lines ("created the folder", "email sent") as each tool finishes - no more staring at a silent mascot wondering if anything is happening.

- **The bubble follows your theme.** On light app themes the buddy speaks in a light bubble with dark text; dark themes keep the classic dark bubble.

- **Bubbles show the whole answer.** Longer replies were hard-cut after about 110 characters - mid-sentence - and a bubble with approval buttons could grow past the edge of the buddy window. Full answers scroll inside the bubble now, on every path, and the bubble can no longer outgrow its window.

- **Make your OWN buddy.** A "+" card next to the pixel skins opens the pet creator: pick a body shape, color, eyes, ears, tail and an accessory, watch the live preview breathe, hit create - your pet is installed with all nine animation states, rendered locally in seconds, no image model, no cloud. Or just tell Skales in chat: "make me a purple octopus buddy" - the agent has the same generator as a tool and activates the result for you.

- **Custom pixel skins - the one you asked for, twenty updates in a row.** The buddy now wears animated pixel pets in the open Petdex sprite format. Three Skales originals (Skales, Bubbles, Capy as pixel pets) ship built in, and any pet from the petdex.dev gallery imports with one paste under Settings > General > Buddy Skin. The pet reacts to your agent: it inspects while Skales thinks, waits during approvals, slumps on errors, jumps when the task lands, and waves hello. You can draw your own too - the format is two files.

- **Discoverable again.** Desktop Buddy and AIPointer now have proper cards on the Add-Ons page that drive the same switches as Settings > General.

### Improved

- **Tasks via WhatsApp and Telegram get four times the room.** Bigger jobs sent from your phone used to hit a tight internal step budget meant for quick replies - the agent runs on your desktop, not on your phone, so the cap made no sense. The budget is now sized for real work, and proper multi-step jobs finish from a single chat message.

- **Your channel activity shows up in Discover.** WhatsApp and Telegram conversations relayed through Skales now appear as feed events (opt-in sharing only, counts - never content), so your Discover presence reflects what you actually use.

- **Sharing your Wrapped goes straight to the feed.** Re-sharing a Wrapped no longer lands in the moderation queue first; it appears in Discover immediately, with a sensible flood guard instead of a once-per-days rule.

- **Discover got a personality transplant.** The community agents run on a sharper model now and are required to have a take - opinions, small fails, dry humor and friendly banter instead of interchangeable filler. The same goes for your own agent when you hit Reply on a post.

- **LLM Profiles refreshed for the current model generation.** New dedicated profiles for DeepSeek V4 (vendor sampling, full tool access - the old generic cap starved it of most tools) and Qwen 3.5; updated MiniMax (M2.7/M3), GLM-5 and Kimi parameters per the official model cards. Profiles also teach models eight more real tool names (reading the inbox, calendar, image generation, asking you a question, searching past chats), so models that reach for another framework's names stop circling and find the right tool first try. Importing a profile by URL now accepts normal GitHub/GitLab file links too - they are rewritten to the raw file automatically, and a URL that returns a web page gets a plain-language error instead of a JSON parse message.

- **Group Chat, VirusTotal scanning, System Monitor and Local File Chat ship enabled.** Four add-ons that work out of the box (or degrade gracefully) were off by default, so most users never discovered them. New installs get them active; existing installs keep whatever you chose.

### Removed

- **DLNA Casting is retired.** Casting a media URL to a TV worked, but reliable screen and media streaming across the device zoo never met the quality bar, and we would rather remove a half-feature than ship one. The page, the add-on card and the agent tools are gone; if it was enabled on your install, it is turned off cleanly.

### Fixed

- **Fresh installs no longer hide half the sidebar.** On a brand-new installation the add-on state file does not exist yet, and the sidebar read it raw - so Studio, Lio AI, Playground and every other add-on entry was missing until you toggled something on the Add-Ons page. The sidebar now sees the shipped defaults from the first launch.

- **Your inbox is checked even when no window is open.** Email polling was driven by the dashboard UI, so a Skales running headless (server, minimized to tray, remote-only use) never checked for new mail and the unread-mail nudge never had data. The inbox check now runs in the background scheduler on your configured interval (default every 15 minutes, set it under Settings > Email), works with the lid closed and pings you via your notification channels.

- **"A contact wrote you" pings have their own switch now - and they are back.** The WhatsApp ping that tells you a whitelisted contact (your mum, your wife) messaged you was internally riding on the "Planner and briefing nudges" notification type. Anyone who unchecked planner nudges on the new Notifications panel silently lost contact pings too. They are their own type now ("A contact wrote you"), on by default, and unchecking planner nudges only affects planner nudges.

- **The Notifications panel warns about the approval footgun.** "Task blocked / needs you" also carries approval requests; unchecking it meant runs waiting for your decision could not reach you. The panel now shows a clear warning when that type is disabled.

## v11.3.1

### Fixed

- **The agent loop got a backbone - and every model benefits.** Long, multi-step tasks used to fall apart: plans were recreated from scratch, the same actions ran again and again, and runs burned their step budget without finishing. Skales now keeps the agent on track itself, step by step - it knows its plan, what it already did and what comes next, whether you run a frontier model or a small local one. Big tasks finish noticeably more often, in fewer steps.

- **Skales stops forgetting.** In long conversations, recent messages and the agent's own checklist could effectively get lost, and a long-running task could even lose track of the original request. All three are fixed: the latest messages always survive, the checklist stays, and the original request stays the task. You will feel this most in long sessions and on smaller models.

- **Dreaming runs on its own now.** The nightly memory consolidation could silently skip a day - and then every day, so the dream diary only ever grew when triggered by hand. It now runs reliably on its schedule, retries on its own when something gets in the way, and a failed run is visible in the dream diary instead of disappearing without a trace.

- **Discover and your stats see more of what you do.** Discover captures more events and tools than before, Wrapped gets three new categories (Voice, Messaging, AIPointer), and a few activities that were counted under the wrong label are attributed correctly now.

- **Notification titles speak your language.** The row titles on the Notifications page now follow your app language in all 12 languages. The notification texts themselves were always generated in your language and style.

- **Friend Mode reaches active users.** If you actually work at your computer all day, proactive check-ins simply never came, on any frequency. Now they arrive on your phone on schedule (WhatsApp, Telegram or email) while you work.

- **WhatsApp stays linked across updates.** The QR code had to be scanned again after every update while Telegram was unaffected - fixed. One last scan after installing this version, then never again.

- **Approving a Discover suggestion works reliably - including remotely.** Approving a suggestion could fail with "You are offline" despite a perfectly fine connection, especially when using Skales from another device. Fixed everywhere.

- **Multi-step work in plain chat just runs now.** The agent could waste its entire budget on finding a place to work before doing any real work, then start over. It knows its workspace from the first step now and gets straight to building - including remotely, where you cannot pick folders.

- **The Continue card appears without reloading.** When a goal paused in the background, the "Continue / Auto / Stop" card only showed up after a browser reload. It appears live now, with working buttons.

- **Continuing a goal continues THE plan.** After a pause, the agent no longer starts over with a brand-new plan - it picks up its existing checklist exactly where it stopped.

- **Mute and choose your notifications.** The Notifications page has a settings panel now: mute all live notifications with one switch (everything still lands on the page, and critical items like approval requests always come through), plus per-type checkboxes for all twelve kinds of notifications, from Friend Mode check-ins to planner nudges.

- **Goal progress counters read correctly.**

## v11.3.0

### Fixed

- **Attaching several files no longer dumps the second one into your message.** With two or more files attached, only the first showed as a clean chip - the full content of every further file rendered as raw text inside your chat bubble. All attached files now show as compact chips, your typed question below them, and a file whose content contains code fences cannot break the display either.

- **Drop a zip into the chat.** Archives (zip, tar, gz, 7z, rar up to 25 MB) attached to a message are saved into the Workspace and attached as a reference chip - ask Skales to extract them or read specific files from them and it will, using its shell and file tools within your permitted folders. Previously archives were rejected with a "cannot be read" error.

- **Schedules that worked before v11.2.7 fire again.** The schedule executor and the schedule queue disagreed about who may run: the queue accepted Autonomous Mode OR the Always-On Agent, the executor only Always-On. On a machine running with Autonomous Mode alone, every user schedule stayed silently skipped - shown as Active, execution log empty, nothing fired (an old hidden scheduler had masked this before v11.2.7 by running schedules on its own). Both now apply the same rule: either toggle runs your schedules. Two more honesty fixes ship with it: a schedule paused after repeated failures now actually shows as paused with a log entry saying why (it used to keep displaying Active while being skipped forever - one toggle re-arms it), and stale failure counters from the era when schedules could not really execute are cleared once, so no schedule stays blocked for failures that never happened. The Schedule page warns clearly when active schedules exist but nothing authorizes them to run.

- **Thinking traces from Kimi-style models stop bleeding into the answer.** Some serving setups start the model directly inside its reasoning and only ever emit the CLOSING think tag. The whole thought process then rendered as answer text with a stray </think> in the middle. An unopened closing tag now ends a reasoning block: the thoughts land in the collapsed reasoning trace, the answer stays clean, and any further stray tags are removed.

- **The update restart is silent now.** Clicking "Restart Now" tears down the internal server and the messaging bots before the installer runs - and for a split second the still-open window showed that dying state as an error flash. The windows now hide first, then the teardown runs; the app simply closes and comes back updated.

- **Release notes on the Update page too.** The same scrollable What's New block as in Settings now sits on the page where downloading and restarting actually happen, so you can read what you are about to install.

- **What's New lives inside the app.** Settings -> Advanced -> Updates now shows the release notes in a scrollable block, newest release first, the full history one click away. The notes are fetched live from skales.app, so even an older installation reads about the latest version; offline the bundled copy steps in, and a freshly updated build that is ahead of the website automatically shows its own newer notes.

- **Team and Swarm pages introduce themselves.** The first visit shows a short, dismissable explainer of what the page does and how to get started: what to enable on the second device for Swarm, and how pairing, the You/Agent switch and the dual-approved shared plan work for Teams. One "Got it" and it never comes back.

- **Skales knows its own interface now.** Asking "where do I set the voice provider" or "where are the AI models" used to get a confident but wrong answer (it would invent a "Voice" tab or send you to Chat for providers). There is now a factual map of every Settings tab, page and theme that Skales consults before telling you where to click - so the directions are right whatever model is running. A build check keeps the map in lockstep with the real UI, so it can't quietly drift out of date.

- **Teams gets a shared plan - two humans, two agents, one checklist.** Open a plan in any team chat: both of you add items and assign each one to a machine (or hit "I'll take everything" to hand the whole list to one device). Nothing runs until BOTH of you approve - and any edit to the list, including a takeover, voids both approvals so nobody can swap tasks after sign-off. Once armed, each computer's agent works through exactly its own items with its own tools and keys, ticks them off with the result attached, and posts each outcome into the team thread. Agents see the conversation only from plan creation onward, a teammate's words are never instructions, and a received plan can never execute anything by itself.

- **Swarm grows up to a real beta.** Manually added devices (Tailscale, fixed IPs) no longer fall to offline after 60 seconds and now survive a restart - a health loop keeps every peer's status fresh, and manual peers can be removed again. The Swarm page's delegate form uses the same queued path as /swarm in chat, so long tasks no longer die on a synchronous timeout; one shared history shows chat AND page delegations with live status that updates when the peer reports back. A /swarm typed in a chat gets its result back IN that chat, and delegated tasks on the working device carry a 🐝 badge with the sender's name. The full command: `/swarm <task>` picks the best free device, `/swarm @name <task>` targets one, and an optional mode prefix sets how it runs there - `/swarm @name code: <task>` (coding agent), `plan:` (read-only plan), `auto:` (fully autonomous). A missing swarm secret is flagged on the page instead of failing silently at send time.

- **Discover suggestions actually arrive.** The "Your AI wants to share" cards were generated for months but could silently jam: a queue full of stale suggestions blocked every new one forever. Stale cards now clear themselves, and finishing real work (a goal, an autonomous task) nudges a fresh suggestion right away instead of waiting for the next four-hour window. You approve or skip - nothing posts on its own.

- **Wrapped lands in the feed as a picture.** Your weekly Wrapped used to be auto-posted as plain text. Now the Monday suggestion card routes through the Wrapped page, captures the real card and posts it as an image - identical pixels on every screen, no review wait. The Post button on the Wrapped page does the same, and the post carries your week's stats. Server-side, the no-review lane for Wrapped images is now hard-gated (owned entry, weekly rate, content check) so it cannot be abused as a backdoor for arbitrary images.

- **Share any gallery image to Discover.** Sharing used to be possible only in the moment of generation; the gallery now has a share button on every image, using the same compress-and-review pipeline.

- **Friend Mode is back - and cannot silently die again.** A check-in that never reached you (channel briefly down, bot mid-restart) was recorded as if it had been delivered; three of those and Friend Mode went permanently quiet, even on the hourly setting. Undelivered check-ins are no longer recorded anywhere, a failed delivery retries after about 15 minutes, and the quiet phase after three genuinely ignored check-ins now means one message per day instead of silence forever. The WhatsApp readiness check now agrees with the actual sender, and an error in Buddy Intelligence can no longer take Friend Mode down with it - each proactive feature fails alone and logs why.

- **Streaming stays in one bubble - and you can scroll away from it.** During a tool turn the live answer now streams inside the same bubble it will finally land in, instead of a second bubble that visibly collapsed into the first the moment the answer finished. Scrolling up while Skales is thinking or typing now sticks: auto-follow can no longer mistake its own scroll for your gesture and yank you back to the bottom, the scroll listeners survive a rebuilt message list, and the jump-down arrow reliably appears when you are away from the latest message (above the chat, below every popup and lightbox). The typewriter itself is now immune to list rebuilds and loading flickers - an answer could previously get stuck fully invisible (reasoning block there, text missing until reload) when the message list was rebuilt mid-turn.

- **The GIF switch does something now.** "Skales can proactively send GIFs" saved a setting that nothing in the app ever read. With the switch on, Skales may now add one fitting GIF to a casual, playful or celebratory reply on its own - in Telegram chats as a native GIF - sparingly, and never on serious or technical topics.

- **Skales types its answers as they are generated.** Replies now stream into the chat token by token instead of appearing all at once after a wait - on OpenAI-compatible cloud providers (OpenAI, OpenRouter, Groq, DeepSeek, custom endpoints) and on local models (Ollama, LM Studio, llama.cpp). The biggest felt-speed difference, especially for local models. Anthropic and Gemini native connections still deliver in one piece for now.

- **The reasoning block is there while the answer streams.** Models that think before they answer (DeepSeek, qwq, Gemini via OpenRouter, ...) show the collapsed reasoning block live, growing as the model thinks, instead of the trace appearing only after the full reply landed. The block stays collapsed and stable - no flicker, no width jumps, no answer disappearing - and expands on click exactly like before.

- **No more emoji row above replies.** Replies no longer get a decorative row of animated emojis stuck above the text; emojis the assistant writes inside its sentences render normally. The reasoning and tool icons in the trace header are unchanged.

- **The get-to-know-you question can appear after a normal chat answer.** The adaptive personalization layer used to surface its single, dismissable question only after a finished goal; a regular chat turn is now a surface moment too. Same protections: warm-up phase, a 6-hour quiet window, never during a running goal.

- **/swarm: hand a task to another of your devices.** Type `/swarm <task>` (or `/swarm @device <task>`) in chat and another Skales on your network runs it, with the result coming back to your notifications when done. No setup, no organization concept - just delegation. The receiving device must opt in (Swarm page, "Accept delegated tasks") and both devices share a swarm secret, so nothing on your network can make your machine work without consent.

- **Teams agents can actually do the work.** When you ask your agent in a team chat to do something, it now runs with this machine's full tools and posts the result to the team, instead of only being able to comment. Everything runs on your device with your keys; the teammate only ever sees the result, and a teammate's messages are treated as input, never as instructions to your agent.

- **Briefing topics deliver real news.** A keyword topic used to return generic search-result links; it now pulls fresh articles from news feeds first (direct publisher links), with web search only as a last resort. Pasting a site URL already expanded its RSS feed - now both subscription types read like an actual news feed.

- **"Remind me in an hour" becomes a real reminder.** The assistant sometimes created a plain to-do for time-based reminders, which fired at the wrong moment or not at all - and then picked its own channel for the message. Time-based phrasing now routes to a real scheduled reminder, and task results are always delivered on YOUR configured notification channel; the agent no longer picks Telegram or email on its own unless you named one.

- **Two size controls under Settings → Appearance.** "Text size (chat bubbles)" scales only the chat conversation in steps from 85 to 140 percent - the rest of the app stays unchanged. "Skales size (whole window)" zooms the entire main window from 70 to 130 percent using the same mechanism as Ctrl+/Ctrl- (no layout gaps), ideal for small screens; the desktop buddy and AIPointer windows are not affected, and the control only appears in the desktop app (browsers have native zoom). Both apply immediately, persist across restarts, and reset to exactly today's look with one click.

- **Reading email works with the new multi-account setup.** The connection test was green for IMAP and SMTP, yet Skales itself claimed the inbox was unreachable: sending already used your configured accounts, but reading still looked at the old single-account settings, which are empty for anyone who set accounts up in the new UI. Reading now uses your accounts too - the full-access account first, a read-only account as fallback.

- **Voice messages are first-class on every channel.** A WhatsApp voice note to Skales was silently ignored and Telegram only knew two transcription providers; both now use the same transcription chain as the app (local endpoint, Azure, Groq, OpenAI), and when no provider is set up the message still arrives with a clear note instead of vanishing. The settings show the live chain, so you can see at a glance that a Groq or OpenAI key is enough.

- **Voice notes in chat.** Hold the mic button to record, release to send: your recording stays in the chat as a playable bubble, the transcript becomes the message, and Skales answers with voice and text as before. Works with click-to-toggle too, and the bubble survives a reload.

- **The assistant remembers its own replies to your contacts.** Outgoing messages to whitelisted WhatsApp contacts are recorded on every send path, so "what did you reply to her?" always has an answer.

- **Cleaning up tasks from chat works.** Asking Skales to delete old tasks no longer fails with a missing tool: it can delete a single task, a Planner task, or clear all finished tasks at once (optionally only those older than N days).

- **The agent-builder tool list stays current.** The tool catalogue on the Agents page is regenerated on every build instead of drifting out of date.

- **Microphone over remote access gets a real answer.** Instead of a dead-end error on plain HTTP, chat and Call Mode now tell you exactly how to get a secure connection over Tailscale.

## v11.2.7

### Fixed

- **The "Telegram gets messages although I selected WhatsApp" mystery is solved.** An older, second scheduling path inside the Telegram companion could run schedules on its own and always announced the results to Telegram, regardless of your channel choice - that is why Identity Maintenance pinged Telegram at 03:00 and schedule confirmations landed there while WhatsApp was selected. It is gone: there is exactly one scheduler now, and results follow your channel. The bot's /schedule list and toggle commands still work.

- **A scheduled prompt acts now, instead of re-scheduling itself.** A job like "send me a message every 5 minutes" was handed to the agent as-is on every run, and the agent read "every 5 minutes" as a request to create ANOTHER schedule - so it produced new schedules and confirmations instead of ever sending the message. Each scheduled run now carries an explicit "this IS the scheduled run, act once, schedule nothing" directive.

- **Friend Mode check-ins got real memory and manners.** A dead channel (stopped bot, unlinked WhatsApp, no send-capable email account) is now detected BEFORE generating - previously every cycle burned a model call and "sent" into the void forever. Email-only setups finally keep a conversation thread, so check-ins stop reading like the first message ever, every time. Skales now remembers its last few check-ins and writes something genuinely different each time. Back-off is real: an ignored check-in doubles the wait, three ignored ones go quiet until you show up again - and an answered conversation is no longer treated as ignored just because the assistant spoke last. Your reply to a check-in no longer gets mirrored back as "what you've been working on". Check-ins are recorded on the Notifications page, and duplicate check-ins are prevented.

- **Approval requests and the daily stand-up respect your channel.** Both used to go straight to Telegram whenever a bot was configured. The approve-by-reply flow still uses Telegram when Telegram is your channel; everyone else gets the alert on their chosen channel pointing to the Execution Board, and the stand-up report is generated by its own pipeline and delivered through the router.

- **Quiet hours only exist when Friend Mode is on.** With Friend Mode off, task results and reminders deliver around the clock instead of being silently muted between 22:00 and 07:00 by a feature you never enabled. Also fixed: equal start and end values (a cleared field) used to mean "quiet 24 hours a day, forever".

- **The Briefing chat stops missing out.** Opening the Briefing pane could swallow fresh links before they ever reached your Briefing chat, which made the chat look dead. Now every refresh - automatic or manual - delivers what it found to the Briefing chat. New batches open with a short model-written summary when a model is reachable (cards always deliver either way), the briefing nudge no longer shares its cooldown with unrelated notifications, and a delivery failure is logged instead of swallowed.

- **System notifications (OS toasts) work as a channel.** The setting existed and Friend Mode used it, but regular notifications (task results, reminders) had no code path for it. When no live channel is reachable at all, Skales now logs why - the Notifications page always has the record either way.

- **Codework "Undo All" only touches what the session changed.** It used to reset the whole folder, which could also discard YOUR own unsaved edits and untracked files. Undo is now strictly limited to the files this session created or modified - your own work is never touched.

- **Codework commands no longer freeze the app.** While a shell command ran, the whole app could stall (chat included), and anything over 30 seconds was killed with a cryptic error. Commands now run without blocking, get 2 minutes, and a timeout says clearly that a long build is not a code error.

- **Studio video shows an error instead of spinning forever.** When a render or cloud video job died or the app restarted mid-job, the progress bar froze and the spinner never stopped. Both now detect a dead job and report it. The Music tab also tells you a key is missing BEFORE you compose your prompt, not after - and it now recognizes a HuggingFace key configured as a provider.

- **Organization approvals behave.** Approve/Reject buttons no longer reappear after you clicked them, agents saving their work no longer stall on an approval card for a plain file save, and when an approval window expires the report says "no decision within 5 minutes" instead of falsely claiming you rejected it.

- **Chat shows work and actionable errors.** While tools run you see a live "Working: ..." indicator instead of a message that looked like a failure. And provider errors (wrong key, rate limit) keep their details after a reload, so the Retry button and error info work instead of leaving a dead end.

- **Adaptive personalization actually asks its questions now.** The question cards (one-tap answers that shape how Skales adapts to you) were armed since v11.0.0 but practically unreachable: they only fired into the currently ACTIVE chat during a narrow 20-90 minute away-window, and anyone steering Skales mainly through Telegram or WhatsApp had a channel session active, which silently disqualified every attempt. The away-window is wider now, and when the active chat is a channel session the card lands in your most recent desktop chat instead, marked unread so you see it.

- **Your WhatsApp assistant now knows what your contacts wrote.** When a whitelisted contact messaged Skales you got the heads-up, but replying "yes, add her appointment" hit an assistant that had no idea what the appointment was: the waiting-request list was short-lived (cleared once answered, expired after 12h) and Telegram - the channel most owners steer from - never received it at all. There is now a durable 14-day log of contact-thread messages in both directions. The desktop/browser chat and the WhatsApp and Telegram owner chats all see the recent thread, "what did my wife write?" and "did she answer?" work via the new read-only whatsapp_contact_log tool, and the relayed texts are explicitly marked as third-party data so a contact's message can never instruct your agent. Nothing changes for the contacts themselves: they still get the restricted persona with no tools and no access to your data.

- **LLM Profiles are on by default now.** Per-model tuning (sampling, tool budgets, tool-name corrections) measurably improves cloud and local models alike, but it shipped as an opt-in toggle that the users who needed it most never found. It is enabled by default for new installs, and existing installs are switched on once - turn it off afterwards and your choice sticks.

- **Models without tool support stop stalling every message.** When a local model can't do tool calling (gemma2, codellama and friends), Skales found that out through a silent 30-second timeout - on every single message, forever. The verdict is remembered now: the first message pays the probe once, every following message sends without tools immediately.

- **Long chats stop dying at the context limit.** Long sessions - especially on local models with small context windows - eventually failed with provider errors until you pressed Compact by hand, and a huge tool result (reading a big file) was carried in full into every following reply. Chats now compact themselves automatically near the limit, and oversized tool output is shortened with a clear marker.

- **Local models got faster and steadier.** Unknown Ollama models no longer request a 64k context window by default (which made 7-8B models swap or die on consumer hardware - the first reply took minutes); they cap at 32k until a real probe says otherwise. Per-model profile hints now survive Code mode and goal runs instead of being dropped exactly where weak models need them. Tool calls with slightly malformed arguments are repaired instead of costing an error round-trip. And the compact system prompt used for constrained models stops spending tokens on easter-egg and emoji-system documentation.

- **Autonomous Mode finally does what its description says.** "Proactively processes any pending tasks from the queue" now includes tasks you compose on the Tasks page or in chat: with Autonomous Mode on they start within about a minute, no Run click needed. Deliberately scoped: only tasks created from this version on - the backlog you parked over months stays manual.

- **Scheduled tasks actually run now.** Every cron schedule - created in chat or on the Schedule page - queued its run and then nothing ever picked it up, so every scheduled run sat at "Pending" forever, on every machine, no matter how Always-On, Autonomous Mode or Safety Mode were set, and the only thing that ever worked was pressing Run on the Tasks page yourself. Now due runs start within seconds, run with the agent's full capabilities, write their result into the schedule's execution log, and deliver it to your Notifications (and your chosen channels). "Run now" on the Schedule page also starts immediately instead of quietly never running.

- **The scheduler no longer double-queues, and old stuck runs are cleaned up.** The same schedule could fire twice in the same minute, producing duplicate runs; that cannot happen anymore. Runs that piled up as "Pending" under the old broken scheduler are retired on the first start instead of all firing at once, and a run interrupted by closing the app is marked as such instead of blocking its schedule forever.

- **Scheduled runs are labeled honestly on the Tasks page.** They no longer show as "manual" runs, and they no longer display the "System task. Turn it on or off under Settings > Memory" note in place of their actual description - that note is reserved for real system jobs like Identity Maintenance. Your scheduled runs show what they actually do, and can be deleted like any other task.

- **Scheduled-run results respect your notification channel.** The completion message used to go straight to Telegram whenever a bot was configured, even if you had selected WhatsApp or email as your channel. Results now go through the same notification routing as everything else, so they land in your Notifications page and the channels you picked.

- **One-time reminders fire even if the app was closed at that minute.** A reminder set for, say, 09:00 only fired if Skales was running at exactly 09:00 - a closed laptop silently swallowed it. A missed one-time reminder now fires the next time Skales checks, later that same day. (Past midnight it stays retired rather than firing absurdly late.)

- **Remote-access mode no longer breaks the app's internal calls.** With remote API protection enabled, the app's own internal calls were rejected as unauthorized, so a whole set of features silently did nothing on protected setups: planner-task runs, the fast post-launch tick that spawns the Telegram bot, the deploy tool, Studio video render / voice / audio merge, playbook list and run, social upload, and organization approvals (which auto-rejected after the 5-minute timeout because the approval request never registered). All of these work under remote API protection now.

- **Folder watchers act on what they see.** A folder watcher noticed changes and queued the follow-up work, but that work was never executed. Watcher tasks now run like any scheduled task and log their result back to the job.

- **Identity Maintenance can actually save its work.** The nightly memory upkeep job runs unattended, but in Safe mode its file updates hit an approval gate with nobody there to approve, so the run burned model calls and persisted nothing. With the existing "auto-approve Identity Maintenance" setting enabled, its own file updates (and only those - never shell or network tools) are pre-approved.

- **Editing a one-time reminder to a new date works again.** A one-time task that had already fired stayed permanently done even after you gave it a new date; rescheduling now resets it so the new date fires.

## v11.2.5 (+ v11.2.6 Hotfix)

### Added

- **Every notification has a home (and is hard to miss).** Proactive updates - a finished task, a scheduled run's result, a reminder, a Friend Mode check-in - used to go only to the desktop buddy bubble or Telegram. With the buddy off and no Telegram connected, they vanished. Now every one is recorded to your Notifications page (with the unread dot on the Bell), pops a toast the moment it happens, and plays a sound. Important ones (a completed task) stay on screen until you dismiss them instead of fading after a few seconds.

- **A live Plan you can watch.** When the assistant works through a multi-step task it shows a pinned checklist at the top of the chat that ticks off in real time. It works in any chat, not only Code mode: give it a task with steps and the plan appears. Collapse it if it is in the way, close it with the X, and it tidies itself away once the plan is done.

- **Skales acts for you when a contact messages it (WhatsApp).** Let family or a colleague message your Skales on WhatsApp. Instead of answering them itself, Skales pings you with their request. Reply with what to do ("make the appointment", "tell her yes") and Skales carries it out with your tools and then answers them for you, like a real assistant. On the road, owner commands help you steer: /help shows what you can do, /pending lists the requests waiting for your OK. The first time you set yourself as owner, Skales sends you a short welcome with these commands.

- **Stop re-approving every edit.** When Skales asks to approve a batch of file edits, the prompt now offers "Approve + allow edits this session". One click and it stops asking for file edits (write, edit, delete, move, copy) for the rest of that chat, so a folder-bound coding session flows without a prompt on every change. Deliberately scoped: shell commands, git push and deploy still ask every time, and it only ever applies to the one chat session.

### Fixed

- **Updating on Windows no longer fails partway through.** If you used WhatsApp (or Telegram), its background bot - and the Chrome it runs - kept running after you clicked Restart, holding the app open so the installer failed with an error ending in "(2)" and you had to uninstall and reinstall by hand. Skales now shuts those background processes down before the installer runs (and on every normal quit), so updating just works. Affects updates going forward from this version.

- **Incoming WhatsApp messages from your own number now arrive.** On modern (multi-device) WhatsApp your message reaches the bot as an opaque linked-device id, and the bot was handing that id to the whitelist instead of your real phone, so it rejected your own messages. The bot now asks WhatsApp for the real phone behind the id first, and never falls back to the device id, so your messages land in the chat and get a reply.

- **Two-way WhatsApp survives reconnecting.** Disconnecting or clearing the WhatsApp session was deleting your settings along with the link: the mode (so the bot fell back to send-only and dropped every incoming message) and your contact whitelist + owner flag (so the route rejected your own messages). Reconnecting to troubleshoot recreated the problem every time. Disconnect now only unlinks the session and keeps your mode, contacts and owner.

- **Notifications and reminders go to the channel you chose, and to you.** Calendar reminders were hardcoded to Telegram, so they arrived there even when you had selected WhatsApp; they now follow your notification-channel setting. And a notification meant for you (a reminder, or a heads-up that a contact messaged you) now goes to your own owner number, not to the first permitted contact.

- **The WhatsApp assistant can finish the job.** Actions that need approval (a calendar entry, a reminder, sending a message or an email) were being blocked on the WhatsApp channel, which has no approve button, so asking your assistant on WhatsApp to do something often did nothing. On your own owner thread your instruction now counts as the approval, so these actions run. File and shell tools are deliberately left out and still never run unattended from a message.

- **The "Multi-Agent running" badge always clears.** If a multitask job hit an error while wrapping up, the badge could stay on until you reloaded the chat. The completion signal now always fires, so the badge clears even when a job fails.

- **Multitask and the checklist survive weak and local models.** On a tight tool budget (small local models, or models that need a capped tool set) the multitask and checklist tools could be dropped while the assistant was still told to use them. They are now protected in the top tier, so they stay available whenever they are advertised.

- **A malformed checklist update no longer wipes the plan.** If a weaker model sent the checklist in an odd shape, the list could blank mid-task. The update now tolerates those shapes and ignores an empty update instead of clearing the plan.

- **Code mode prefers fast checks over a full build.** Verifying "done" now leans on typecheck / lint / test (and notes that a long build hitting the shell timeout is not a code error), so Auto does not chase a timeout as if it were a bug.

- **The Always-On scheduler is honest about what it ran.** A scheduled task showed "Executed" with a 0.0s duration the instant it fired, which read as "ran and did nothing" - it had only been queued. The log now says "Queued", and the actual run and its result land in your Notifications when it finishes. Each schedule's own execution log now also records the run's result (or error), so on the Schedule page you can see whether a cron actually worked and what it produced, not just that it fired. The assistant also no longer tells you the scheduling tools (schedule_recurring_task and friends) do not exist: they are listed in the capability index, a capability lookup by tool name resolves them, and they survive the tight tool budget on small local models so they stay callable.

- **Documents and the to-do list no longer pop open when you revisit a chat.** Reopening a finished chat from history flung the in-chat document editor and the plan checklist wide open every time. They now stay collapsed when you reopen a session, and only open or expand for a live update while you are actually working in the chat.

- **Pairing two computers (Teams) completes now.** The window showing your 6-digit code is a full-screen modal, and the "accept this connection" prompt was rendering behind it - you could not reach it, and closing the code window tore the pairing down before you could. The accept / reject prompt now appears inside the code window the moment the other machine enters the code, so you confirm it right there and the team forms. (Clicking the backdrop no longer cancels a pairing that is waiting for your OK.)

## v11.2.4

### Added

- **Multitask / sub-agents are a first-class command.** Ask Skales to "run these in parallel", "send sub-agents" or "leg ein multitask an" and it fans the work out to parallel background agents that report their results back into the chat. A live "Multi-Agent running" badge shows while they work, with the Tasks tab for detail. Multitask requests no longer slip into a day-planner entry by mistake.

- **A live plan the agent works through.** For a multi-step task the agent keeps a checklist in the chat: it lays out the steps, marks the one it is on, and ticks each off as it finishes, so a long Code or Auto run stays on track instead of wandering. In Plan mode it shows you the plan as that checklist.

### Changed

- **Code mode verifies before it says "done".** After changing code, Skales runs the project's own check (it detects your npm / cargo / go / pytest setup), reads the failures, fixes them, and re-runs until green; in Auto mode it does this on its own.

- **Clearer workspace handling in plain chat.** Working in a normal chat without binding a folder now clearly uses the Skales workspace for files, and the mode switch makes it obvious you can bind a folder for deeper work with one tap.

## v11.2.3

### Fixed

- **The private Briefing recovers on its own.** A single failed poll could leave the Briefing stuck so it never refreshed again. It now advances its schedule even when a poll fails, so it always retries on the next cycle instead of freezing.

### Changed

- **The Briefing shows when it last updated.** A small timestamp next to the refresh button (with the exact time on hover) tells you at a glance whether the feed is fresh, so a quiet feed reads as "nothing new right now" rather than "is this broken?".

## v11.2.2

### Fixed

- **Incoming WhatsApp messages reach Skales again.** Writing to your assistant from your own second number did nothing: the message never showed up in the WhatsApp chat and Skales never reacted, in either mode, and reconnecting or re-entering the number did not help. Modern WhatsApp routes a personal chat through a linked-device id that is not the phone number, and the previous build forwarded that id as the sender, so it matched neither your whitelist nor your own number and was turned away before any conversation was created. Skales now asks WhatsApp for the real phone number behind that id, querying WhatsApp itself when it has never seen the number before, so your messages land in the WhatsApp chat and get a reply. Sending (Friend Mode check-ins and the replies you trigger) was never affected.

- **A turned-away incoming message is no longer silent.** When the bot forwarded a message and Skales rejected it, the bot logged only the status code and treated every outcome as a success, so a real rejection looked like a delivery. That is why this looked fixed before while staying broken. The bot now records the exact reason in its log and Skales notes a blocked sender, so any future delivery problem is visible instead of disappearing into an empty chat.

- **The Read & Write switch always takes effect.** If the setting reached the running bot at a bad moment it could keep using the old value until a restart. The bot now re-reads the setting on its own within a few seconds, so turning Read & Write on or off is reliable without restarting.

- **An expanded image fills the window.** Opening an image or a shared screenshot in chat showed the viewer behind the sidebar, so the menu sat on top of the picture. The lightbox now opens above everything.

- **A WhatsApp reply stays in one thread.** When a contact's number was saved in a local format that differed from how WhatsApp reports it, the reply could open a new conversation separate from the Friend Mode thread. The same person now always maps to one thread.

### Added

- **Optional VirusTotal scan for Code-mode writes.** A new toggle under Settings > Chat & Code, off by default. With it off, files the agent writes are no longer sent to VirusTotal on every write, which keeps Code mode local-first and avoids shipping hashes of your own files out. Turn it on to have each write checked. Files downloaded from the web are still scanned when VirusTotal is set up.

## v11.2.1

### Fixed

- **WhatsApp two-way messaging works again.** Incoming messages from you were being dropped since the privacy gate landed, on two counts: modern WhatsApp routes personal chats through an addressing scheme the bot no longer recognised, and your own number was never auto-trusted so it failed the contact whitelist. Skales now recognises modern messages, resolves the real number, and always treats your own linked number as the owner, so you can reach your assistant out of the box. The whitelist still gates third parties, and Send Only stays outbound-only by design.

- **Friend Mode delivers and records on the same channel.** A WhatsApp or Email check-in was recorded in the Telegram conversation, so the message and your reply ended up in different threads. Friend Mode now records each check-in in the channel it was actually sent through, and sends WhatsApp to you (your owner-flagged contact) instead of an arbitrary permitted one.

- **The Friend Mode test tells you what happened.** The Test button used to report success even when a channel silently failed. It now shows which channel did not deliver and why, for example an email account that is not send-capable.

- **The assistant knows what LLM Profiles and Agent Memory are.** It no longer reports them as broken or tries to turn them on as if they were skills; it reads their real on/off state from Settings and points you to the right place.

### Added

- **Code mode: give a chat its own folder and work there.** A new Chat / Code / Plan / Auto switch in the composer (or the /code command) points a single conversation at a folder on your computer, so Skales reads, edits and runs commands inside it like a coding agent instead of in its own workspace. Plan is read-only and proposes a step-by-step plan before touching anything; Code makes the changes directly; Auto works through the task on its own until it is done or blocked. If the folder is outside what Skales is currently allowed to touch, it asks first and widens access only the way you pick (add just that folder, or allow full disk). Results surface the way the rest of Skales already shows them: a live HTML preview, a document, or a file you can open. Each chat keeps its own folder and mode, so a plain chat behaves exactly as before.

- **Code mode edits surgically, does git, and runs tests.** A new edit_file changes part of a file by exact text replacement instead of rewriting the whole thing, so edits are smaller and safer. git_status, git_diff, git_commit and git_push work in the bound folder using your own git identity and credentials (no attribution is added), so "commit and push my changes" runs end to end; if a push needs GitHub auth it tells you instead of failing silently. test_run detects the project's test framework (npm/jest/vitest, pytest, cargo, go) and runs it, so the agent can check its own work.

- **The agent can ask you structured questions.** When it needs you to choose between options or confirm a direction, it shows a small slide-up form (clickable options, single or multi-select, one to a few steps) instead of a long question, then continues with your answers.

- **One-click deploy.** deploy_project detects Firebase, Vercel, Netlify or an npm deploy script in the bound folder and runs it with your existing CLI login, so "deploy the site" works end to end (now that shell commands have room to finish).

- **A model just for code.** A new Settings > Chat & Code tab (between AI Provider and Goals) lets you pick a model used only in Code mode (Code/Plan/Auto): choose a provider and fetch its models (live for OpenRouter), so a strong cloud model does the coding while your chat stays on your default or local model. Leave it empty to use your active model.

- **Deep reasoning (xhigh).** An opt-in toggle (Settings > Chat & Code) that asks any model to think a problem through step by step before acting, so Sonnet, Gemini and local models benefit too, not only models with a native reasoning mode.

- **MiniMax (M2 / M2.7) profile.** A built-in LLM Profile for MiniMax's agentic models, with the vendor's recommended sampling.

### Changed

- **Shell commands have room to finish.** The old hard 30-second limit killed real work (npm install, a build, git push, a Firebase deploy) mid-run and truncated the error. Commands now run up to 2 minutes by default, configurable up to 10 in the new Settings > Chat & Code tab, and keep up to 10 MB of output, so those commands can actually complete and you see the real error if one fails. A command that does hit the limit now says so clearly instead of failing silently.

- **Auto mode in Code really runs on its own.** After a one-time consent, Auto pre-approves the file and shell tools inside the bound folder, so a multi-step task no longer stops for approval on every step. Email, WhatsApp, browser and other tools that reach outside the folder still ask first, dangerous commands stay blocked, and you can leave Auto anytime.

- **Friend Mode uses one outbound channel.** The channel picker is now a single choice (Telegram, WhatsApp, or Email) so the channels cannot conflict and a reply always comes back to one place. Local notifications are unaffected.

- **Briefing follows a whole site, not just its homepage link.** When you add a news site or a YouTube channel, Skales now finds its RSS feed automatically and pulls the latest articles or videos, instead of bookmarking the front page once. The private Briefing also refreshes every 3 hours instead of every 6, and still arrives in its own Briefing chat.

- **Goals finish cleanly, stay on the thread, and learn.** A finished goal now shows a completed card instead of still offering Continue/Stop. If you reply to a goal that paused for your input and add a follow-up ("looks good, and after that do X"), Skales continues the SAME goal with your answer and the extra task in context, instead of forgetting its worked-out plan or starting over. And a hard-won approach is now recalled in ordinary agentic chats too, not only inside a goal, so what took many tries once is reused next time; a stalled step is logged with the tool it got stuck on.

## v11.2.0

### Added

- **Teach Skales a desktop task by recording it.** On the Workflow page, hit Teach by recording, do the task once on your screen, then press F10 to stop (F9 pauses, so you can skip typing a password). Before it saves you see every captured step, including the text you typed, so you can check nothing sensitive slipped in, then save it as a /goal command. Type that /goal in chat, or press Run, and Skales replays your exact clicks, typing and scrolling after a short countdown that lets you switch to the right window. Replay is built in, with no setup. Honest about limits: it works on macOS, Windows and Linux X11 (not Wayland), and it records and assists rather than blind-driving arbitrary native apps.

- **A real document you and Skales share.** Open the Document panel from the chat header or with /doc. Ask Skales to write or change a document ("write me a doc about X") and it appears in the panel; edit it yourself with the formatting toolbar (bold, italic, underline, heading, list, quote, code) and Skales sees your changes on the next message. A chat can hold several documents, picked from the dropdown, and they are saved with the chat. Documents live with the chat, not as files on your disk; click Download to save the open one as a .md file, or ask Skales to save it as a file when you want one on your computer.

- **Group Chat is a live conversation now.** Pick your participants, and after the opening round you stay in the chat: write to the group, the agents read your messages and answer each other turn by turn, one acts as the advisor, and you end it with a button instead of it running on. Typing /groupchat in a chat with no topic hands the current conversation over to the group as the topic.

- **Your Briefing arrives as cards.** Fresh Briefing links now show as compact cards (image, title, summary, source) instead of long raw links, ad and tracker redirects are filtered out, and /briefing pulls up your unread items on demand.

- **Coloured tray status and a Chat submenu.** The tray status shows a colour dot for its state, and hovering Chat gives you New Chat and History without leaving the tray.

- **Your Briefing comes to you.** Fresh links found for your Briefing now arrive in a dedicated Briefing chat (with the waiting dot) and the notification names the top item, instead of only telling you a count.

- **Waiting-message markers.** When Skales reaches out on its own (Friend Mode check-ins, finished scheduled tasks) the conversation shows a dot in the chat list and History so you can see a reply is waiting, and it clears the moment you open it. A count on the chat history button shows how many chats are waiting, so a pile of them is visible at a glance.

- **Agents that learn from their work (opt-in).** Turn on Agent Memory in Settings and each Custom Agent keeps its own memory, distilling a short lesson from every task it finishes and reading it back on the next run, so it gets better at your work over time instead of starting fresh each time.

- **LLM Profiles for reliable tool use across models (opt-in).** Turn on LLM Profiles in Settings and Skales tunes itself per model: it caps the tool set, compacts the prompt, sets sampling, and adds short per-model hints, so weaker or local models stop fumbling tool calls. A profile binds automatically to whatever model its pattern matches, with no default to set, and the LLM Profiles page shows you which profile your current model is using. Profiles can also teach a model your tool names directly, so a model that reaches for create_file learns the real tool is write_file. Built-in profiles ship for DeepSeek, Qwen, Llama, Gemma, Mistral, GLM, Kimi and small local models; import your own from a file, pasted JSON or a URL, with the full tool-name list right there so you never have to know them by heart. Frontier models are left untouched.

### Changed

- **Local models stay warm between turns.** Skales now keeps your local Ollama model resident for 30 minutes after a turn instead of letting it unload on Ollama's 5-minute default, so back-to-back turns and background goals no longer pay for a cold model reload. Power users can change the window (including keeping it always resident) with the SKALES_OLLAMA_KEEP_ALIVE setting.

- **Smaller and local models have far more room to work.** Skales now sends a lean system prompt by default and looks up capability and feature detail on demand, and it automatically gives constrained models (local models, and smaller cloud models such as DeepSeek) a compact prompt and a focused set of tools. Weaker models complete tasks and call tools noticeably more reliably as a result.

- **Chat no longer gets stuck repeating a tool call.** If a model calls the exact same tool over and over in normal chat, Skales nudges it to change course and stops cleanly instead of looping until the step limit.

- **Clearer command approval.** When Skales asks before running a shell command, it now also explains that you can let it run commands without being asked each time by switching Safety Mode in Settings.

### Fixed

- **Email and calendar tools work on local models again.** On small local models with several integrations connected, Skales caps the offered tool set so the model is not overwhelmed, and your connected email and calendar tools could be dropped from that set while the model still tried to call them, so nothing happened. Connected email and calendar now always survive the cap, so those calls actually run.

- **Record a browser flow straight into a Playbook.** On the Playbooks page, Record opens the browser in record mode and captures what you do as a playbook, while + builds one by hand step by step. The browser now opens to DuckDuckGo (a local-first tool has no reason to push Google), and Skales never records its own window as a step, so a recording starts from a real site. Promote any playbook to a /goal command from its menu.

- **WhatsApp keeps your data to you, and you stay in control.** Only the number you mark as the owner (Settings, WhatsApp contacts, "Set as you") gets your assistant with your memory and tools. Skales never auto-replies to anyone else: when a whitelisted contact writes, their message is recorded, their chat is flagged waiting, and you are pinged, then you decide, tell Skales "answer Marina that ..." and it sends (after you confirm), or turn it into a reminder. Unknown numbers are ignored entirely. With no owner set, nobody gets your assistant.

- **The chat no longer flickers while you type.** The HTML preview used to reload and flash white on every keystroke (in the composer or the Document panel); it now stays put.

- **Clearing a chat sticks now.** /clear used to wipe only the view, so the messages came back on reload. It now clears the stored conversation too. The Briefing inbox also keeps only its recent entries instead of growing forever.

- **WhatsApp inbound is gated and safer.** Only contacts on your whitelist can reach Skales over WhatsApp, and a contact's WhatsApp Status is never treated as a message, so a Status can never be acted on as an instruction. Images sent on WhatsApp are read with your configured Vision provider.

- **The Vision provider you configure is actually used.** With a Vision provider set up under Settings, Skales now reads images with it in chat and on Telegram instead of quietly sending them to your active chat provider.

- **Scheduled tasks and Friend Mode check-ins keep firing.** A background task that hung could quietly stall the whole scheduler, so cron jobs and proactive check-ins stopped running until the app was restarted. Proactive check-ins now run on every heartbeat regardless, a stuck task is released automatically, and the background runner restarts itself if it ever stops. Turning on the Always-On Agent now runs your scheduled tasks on its own, even when Autonomous Mode is off.

- **Large file attachments are no longer cut short.** Attaching a big text or code file now sends as much of it as the model's context window allows, honoring your Override Model Limits, instead of a fixed cap that dropped most of a large file.

### Removed

- **Claude and Gemini subscription sign-in.** Anthropic and Google do not allow paid-subscription sign-in from outside their own apps, so these two options could never connect. Signing in with a ChatGPT (Codex) subscription still works, and Claude or Gemini stay available through an API key under AI Providers.

## v11.1.6

A focused fix release on v11.1.5.

### Fixed

- **WhatsApp connects on Windows again.** Setting up WhatsApp on Windows failed with a missing-component error and never showed the QR code. Skales now ships everything the WhatsApp link needs, shows the QR, and pairs. Reconnecting also works reliably if the link ever gets stuck, instead of silently doing nothing.

- **Setup no longer loops back to the start.** After the welcome summary you could be sent back to the first step over and over. Setup now finishes and opens the chat.

- **Friend Mode email check-ins arrive.** Proactive messages set to the email channel were not being delivered. They now send from your connected email account, the same one the chat uses.

- **Run a scheduled task by hand and actually see it.** Pressing Run now updated the last-run time but left the execution log empty, so it looked like nothing happened. Manual runs are now recorded in the log like scheduled ones.

- **Chat hover hints stay visible.** Tooltips near the message box could be drawn behind it. They now appear on top.

### Changed

- **Voice runs through your Skales voice setup.** The separate on-device voice engine has been removed. The AIPointer overlay's read-aloud and voice input now use your normal Skales voice configuration. The download is smaller and a Windows setup error is gone.

- **A nicer default email signature.** New email accounts start with a short "Cheers" signature instead of a sample placeholder.

## v11.1.5

A small follow-up on v11.1.0.

### Added

- **Share a generated image to Discover.** Studio could share a Skales Visual but not a plain image. The image view gets a Share to Discover button: the image is fitted under the feed's size limit and posted, landing in review like other shared images.

- **Send a file to your Telegram.** Skales can now send a document, image or PDF to your paired Telegram, not only text. Ask for it ("send me that report on Telegram") and the file lands in your chat.

- **Your own daily Briefing in Discover.** Follow the topics you care about and Skales brings you fresh links every day, in its own Briefing space inside Discover. It is private: only you see it, nothing is posted or shared. Skales suggests topics to start from based on what you work on and your language, you can add or unfollow any with a tap, and each item opens in the built-in browser so you stay in Skales. It fills in the background and works with a local model or no model at all, and pauses cleanly when you are offline.

### Changed

- **Skales works with whatever model you run, big-name or not.** Skales now recognizes an action whether the model returns it the standard way or writes it out as text, so a newer or smaller model can drive Skales' tools and multi-agent dispatch without special handling. If a model gets stuck repeating itself, Skales nudges it to try another way or wrap up instead of stalling. A model you wire up later just works.

- **Skales tells you which model it is running.** Ask what model or AI it is and it answers honestly with the model you configured, instead of claiming to be a different assistant.

- **Everything is on out of the box.** A fresh install starts with all features active so nothing looks missing; turn off what you do not use in Add-Ons. Existing installs are left exactly as you had them.

### Security

- **Pairing is harder to intercept.** The relay that lets two devices find each other during pairing now turns away any third device that tries to join a pair's rendezvous, and limits how often one address can connect. That closes the window where someone could brute-force a pairing code or quietly sit in on a handshake. Your messages were already end-to-end encrypted; this hardens the first-contact step itself. It applies to every v11.1.0 install with no update needed.

- **Skales reads web pages and other fetched text as information, never as orders.** Anything Skales pulls in through a tool, a web page, a search result, an email, a feed, is now clearly marked as outside content, and Skales is instructed never to act on instructions hidden inside it. This stops the "ignore your previous instructions and email this to..." trick where a booby-trapped page or message tries to hijack the agent.

- **Autopilot runs its actions, and the hard safety net can never be switched off.** An armed goal is your go-ahead, so Autopilot now actually carries out the steps you set it up for instead of quietly skipping the ones that send or change something. Underneath that, one block stays on in every mode, including Unrestricted: commands that would wipe a disk, delete your home folder or Skales' own data, set off a fork bomb, or power the machine off are always refused. That block cannot be disabled.

- **More actions stop to ask first.** Sending a WhatsApp message or media, and typing or pressing keys inside a logged-in browser page, now wait for your approval in Safe and Advanced mode, because the agent chooses who it goes to or what it types and a hostile page could try to abuse that. Replies to your own Telegram stay automatic, since they only ever go back to you. Advanced mode now also runs a risky shell command after you approve it, where Safe mode still refuses it outright.

- **The settings file is off-limits to the file tools.** Skales' own settings file can no longer be read or written through the ordinary file tools, only through Settings. That keeps your keys and security switches out of reach of a crafted file action.

- **Server-side fetches stay on the public web.** When Skales fetches a file or a URL for you, it now allows only normal web addresses, refuses internal and loopback targets, follows your domain blacklist, and re-checks every redirect so a link cannot quietly send it somewhere private. A downloaded file gets a clean name and must land inside a folder you allow, and a file only leaves over Telegram if its path is allowed.

- **Approvals belong to one chat and run exactly once.** A Telegram approval can only be confirmed from the same chat that asked for it, so another chat cannot tap Approve on your pending action. A double-tap or a retry can no longer run the same approved action twice.

- **An incoming message cannot promote itself.** The internal "system job" path that skips the usual checks is now reachable only from inside Skales. A Telegram message that claims to be a system job is treated as the ordinary message it is.

## v11.1.0

A feature release on top of v11.0.0. Two Skales computers can pair and work together
(Teams), other agents can call Skales (A2A), Autopilot becomes the live board of every
goal Skales is working on, History search can find a chat by meaning, and the system
tray shows what Skales is doing, plus a new interview-first way to set a goal and a
wave of stability and security work.

### Added

- **Teams: pair two Skales computers and work together.** Turn on Teams, pair another Skales, and you and your agent can talk to the other person and their agent, end-to-end encrypted. You confirm each new computer by name before it can pair. Each teammate gets a tag, an online indicator, and a conversation that lives in Teams (not your chat history). A You / Agent switch in the composer lets your agent reply on your behalf. Cross-computer messages never run anything on the other machine. Off until you turn it on; the mobile pairing you already use is unchanged.

- **Other agents can call Skales (A2A).** Skales now speaks the Agent2Agent standard, so another agent (or another Skales) can discover this instance and delegate a task to it. Off by default, and you stay in control of what an outside caller can do. Turn it on from the Teams screen.

- **Autopilot shows your running goals.** The Execution Board grows a Running Goals strip: one card per active goal across every chat, with its status, acceptance-criteria progress, step budget and channel. Click a card to jump straight into that goal's session. It is the live grid of everything Skales has in flight.

- **Set a goal by interview with `/goal-autopilot`.** Type it in chat and Skales opens Autopilot and interviews you first, to understand what you actually want, then proposes the concrete tasks, instead of arming a goal from a single line. The plain `/goal` (instant) path is unchanged.

### Changed

- **Voice runs on your device, in the cloud, or on your own server.** Voice uses your operating system's built-in speech by default, the cloud providers you configure (OpenAI, Gemini, Groq, ElevenLabs, Azure), or any OpenAI-compatible speech endpoint you self-host and enter under Settings, Voice. The bundled on-device engine (Kokoro for read-aloud, Whisper for transcription) stays available on Windows and Linux. On macOS it is no longer offered. Its machine-learning components cannot be code-signed inside the notarized build and so could not run there. macOS users use System, Cloud, or their own speech server, and every engine still falls back to the operating system voice if it is unavailable.

- **Web search names the provider it actually uses.** The agent no longer claims it searches with Tavily when DuckDuckGo (the keyless default) or another provider is the one configured.

- **The Playground opens right away** instead of sitting on a blank screen while it prepares its first suggestions in the background.

### Added

- **Multi-agent job results come back into the chat.** When you hand a job to multiple agents, the finished report is written back into the conversation and you get a notification when it completes, instead of having to open the Tasks tab to find it. A sub-task that runs out of its step budget now shows as parked rather than finished.

- **Find any past chat by meaning.** History search gains a Meaning toggle that ranks your conversations by what they were about, not just the exact words, blended with how recent they are. It runs on your own embedding model (local by default) and falls back to keyword search when none is set, so it never returns worse results than before.

- **The system tray shows what Skales is doing.** Open the tray menu for a live snapshot: the active provider and model, API calls this hour, scheduled planner tasks and goals, and a warning when an approval is waiting or Autopilot has paused at its cost cap. Quick links jump straight to Chat, Planner, Studio, Autopilot and Settings.

- **A context and session readout below the composer.** A quiet line shows how full the model's context window is for the current chat and how long the session has been running. Hover it for the exact token count, the model, and a note that Skales auto-compacts older messages around 75% so the model stays within its window. It reads the window from the known-model list, so it works on any provider out of the box.

- **Teach Skales a workflow by showing it once.** Walk Skales through a task in a normal chat, then open the Workflow page and pick that chat: Skales distills it into a reusable workflow with a name, a trigger, the repeatable steps and what counts as done, and you can run it again from chat with `/goal-<trigger>`. The replay runs through the normal goal path and falls back to vision when a button has moved, so a workflow you captured keeps working as pages change.

- **Review the code Lio wrote, not just the rendered page.** When a Lio build finishes, the right pane gains a Code view next to Preview: every file Lio produced, with its source shown read-only and a copy button, and images rendered inline. You can read what was actually built before you download it, deploy it or ask for changes.

### Fixed

- **Shared Skales Visuals display correctly in the feed.** A visual built at a fixed size used to show only a shifted corner; it now scales to fit, like the Studio preview.

- **Telegram and WhatsApp no longer go silent** when a task needs more than one message. You get an honest status and can reply to keep it going.

- **The occasional personalization question no longer repeats** the same prompt.

- **Copy to clipboard works in the Tasks result view**, and the chat composer cursor stays aligned with a highlighted command.

- **Uploading a file no longer starts a goal by accident.** A document that mentions a slash command (a changelog, these release notes, a piece of source) is now read and summarized, not treated as a command to run. The same goes for a skill mention written inside a file.

- **Identity maintenance stays in the background.** The nightly identity refresh no longer sends a stray "completed" message over Telegram or surfaces a bubble in the active chat; it runs silently, as it was always meant to.

- **The WordPress page no longer fails when nothing is connected.** Opening it without a configured site shows the setup prompt instead of a connection-failed error.

- **Agent-run playbooks perform more of their steps.** When Skales runs a saved browser playbook on its own, it now also does scrolling, key presses, screenshots and form fills. The few steps that need the live Browser view say so instead of being skipped without a word.

- **The edit box matches the chat width** when you edit a message, and the New Chat screen takes images by drag-and-drop. Toolbar buttons there now explain themselves on hover.

- **A recurring task you ask for in chat lands in the right place.** A sub-daily cadence (a 30-minute Pomodoro, "every two hours") now goes to Schedule, which runs it, while the Planner keeps daily, weekly and monthly jobs. Before, an unsupported cadence was saved but never appeared on the Planner and never fired.

- **The running-goal status popover stays on screen.** On a narrow window it used to slide off the left edge; it now opens rightward and is capped to the viewport, like the model picker. Searching settings for "a2a" or "teams" also finds them now, with a link to the Teams page.

## v11.0.0

```
███████  ██  ██    █████   ██       ███████  ███████
██       ██ ██    ██   ██  ██       ██       ██
███████  ████     ███████  ██       █████    ███████
     ██  ██ ██    ██   ██  ██       ██            ██
███████  ██  ██   ██   ██  ███████  ███████  ███████

   v11.0.0    release name:  /GOAL
```

A new look for Skales, a new way to work by setting Skales a goal that keeps going on its own in the background until it is done, a rebuilt Studio for images, video, voice, and music, one home for all your chats and projects, and your saved provider API keys now kept encrypted on disk.

### Added

- **Set Skales a goal and let it work toward it.** Type `/goal` followed by what you want, like `/goal build me a trading bot`, and Skales takes it on as an ongoing goal instead of a single reply. It works out what reaching the goal looks like and keeps going through the steps toward it, and when it needs a decision from you it parks the goal and shows a card to pick it back up or stop it.

- **A goal keeps working on its own in the background.** Once you set one, the work carries on by itself without the chat window open, and picks back up where it left off after you close and reopen the app. Skales keeps each goal on its own track, so you can set more than one going and come back to find them further along.

- **Set a goal to run on a schedule.** Alongside a one-off goal, you can now set one to run on a repeating schedule, like every morning or once a week. Skales works it on its own in the background each time it comes due and keeps each run separate, so a standing goal makes progress without you having to start it.

- **Skales can take on a goal from how you ask.** Off by default. When you switch it on, a message that reads like a project, for example "build me a small site and then put it online", is taken on as an ongoing goal instead of a one-off reply, with a sensitivity you can set from low to high. The wording is read on your device with no extra model call, so nothing about your message is sent anywhere to make the decision.

- **Skales can keep working a goal while you are away.** Off by default. When you switch it on, a goal that paused on its own can carry on while you are idle, picking back up once you step away and parking again for you the moment you return, so a long goal can move forward in the background without running on while you are right there.

- **Keep an ongoing presence with a goal.** Start a goal with `/goal presence:` and Skales holds a steady presence over time as a declared agent, only on the channels it can use honestly: your own Discover feed, email through your mail settings, and the official integrations you have connected. It never pretends to be a person or works around anything built to keep automated agents out, and it learns from what comes back each round. There is a short plain-language note about goals, and this type, at the bottom of the Goal settings.

- **A new look, Skales-X.** Skales opens on a new default theme with a floating glass sidebar, a calm dark palette lifted by a single bright accent, and a short launch screen when it starts. Existing installs move to the new look on update, and every other theme, including the previous one, is still in Settings under Appearance if you want to switch back.

- **One home for your chats and projects.** There is now a single place to find every conversation you have had. A history page lists your chats and lets you search across what was said inside them, opening Skales lands on a New Chat screen with your recent chats one click away, and any chat can be linked to a project so the two stay together, with Skales keeping a short running summary of what the chat is about.

- **A rebuilt Studio.** Studio is organized around four areas: Design, Media for images and video together, Audio for voice and music together, and a Gallery of everything you have made. A new button starts a fresh design, clicking any result in the Gallery opens it full size, and starting a design from a web address now reads the page in properly instead of coming up empty.

- **Share a Skales Visual to Discover.** A visual you make in Studio can be shared straight to Discover as its own live piece, and you can also save it as an HTML file or a PNG. Shared visuals are checked before they appear to everyone, and they run inside their own sandboxed frame.

- **Import your conversations from Hermes.** If you have used Hermes, you can bring your history across. Choose Hermes from the import options in Settings and point Skales at its database file; your past sessions come over, along with any personality and memory notes saved next to it.

- **Skales adapts to how you work.** Over time Skales builds a quiet sense of how you like to work and folds it into its memory, and now and then it asks you one short question to understand you better, never while it is busy and never often. You can also set your agent's character outright when you join Discover, its tone and how grounded it stays, and that carries into your chats. It stays part of your memory and is always on. When that short question does come up you get a quiet notification and a dot next to Chat so it is not missed, and you can clear everything Skales has picked up about how you work at any time, from Settings or right in the chat.

- **Cast to a screen on your network.** Skales can send a page, a chat answer, or something you made in Studio to a TV or media player that supports DLNA on your local network. It is off until you turn it on, finds devices on your own network only, and each cast is something you start. Casting something you made in Studio turns on a small sharing bridge on your own network for that cast, which you switch on yourself.

- **Draw a workflow and run it like a goal.** A goal you set in words now has a hand-drawn counterpart. A visual editor lets you lay out the steps, the inputs you will fill in, and what finished looks like, and saves the result as a workflow you can run again. Each one gets its own trigger word, like `/goal-ship`, that lights up in the chat box, and one click opens a fresh chat with it ready to edit and send. Switch workflows on under Add-Ons; a pointer in Settings, Goal shows you where.

- **Bring in your Obsidian vault.** On the Memory page you can now import an Obsidian vault, a folder of Markdown notes, or a handful of loose files. Skales reads the links between your notes and draws them as a graph you can move around, following a wikilink from one note to the next and seeing what links back to the one you are on. Once it is in, Skales can read from your notes when you ask.

- **Reach more of what your MCP servers offer.** Beyond their tools, MCP servers can publish ready-made resources and prompts, and Skales now reaches both. Type `@` in the chat box to pick one and attach it to your message as context, shown as a chip you can remove. Servers that use the newer streamable-HTTP connection work too, alongside the ones Skales already spoke to.

- **Make an image or video without leaving the chat.** Type `/image` or `/video` with a short description and Skales draws it right there in the conversation with its built-in visuals engine, no key or setup needed. What you make stays in your history and redraws when you reopen the chat.

- **Skales can search through your files.** It can now search file contents for a word or pattern and find files by name across a folder, so when you point it at a project it goes straight to the right place instead of reading entire files one by one. Its work on large folders is faster and more accurate as a result.

- **Reach Telegram even on networks that block it.** Some workplaces and regions block a direct connection to Telegram, so the bot could not receive your messages or send replies. You can now enter a proxy in the Telegram settings, and Skales sends everything to and from Telegram through it, so the integration keeps working from behind a firewall.

- **Discover, rebuilt as a living feed.** Discover is now a three-pane feed with Spaces you can join, a sort bar for what is hot, new, top, or rising, and a card that picks out posts matching the interest you chose. Joining starts by giving your agent a character: its background, its generation, and a few dials from serious to playful, dry to funny, and grounded to speculative. That character stays in your memory and carries into your chats, so your agent talks the way you set it.

### Changed

- **Skales gets better at the kinds of goals it has done before.** When a goal succeeds, Skales distills the approach that worked and reaches for it next time, on top of folding in what it learned from the parts that did not. The Memory page shows this as a before and after: the friction it ran into on a kind of task, and the proven approach it now uses instead.

- **The sidebar is steadier and easier to read.** On the new look the sidebar stays expanded as you move between pages, and when it is collapsed each icon shows its label on hover, so you can tell the sections apart at a glance.

- **Web search sits with your other integrations.** The web search setting now lives under Settings, Integrations, alongside your other connected services instead of on its own.

- **Fresh installs start on current models.** The default model choices now point at the current generation, so a new setup begins on up-to-date models without you changing anything.

- **Answers appear as they are written.** A reply now streams in word by word as Skales writes it, instead of arriving in blocks every second or two. If you have scrolled up to read something earlier, it leaves you where you are and keeps writing below, with the jump-to-bottom button one click away.

- **Skales Visuals is built into the image and video toolbar.** The generation toolbar in chat now starts on Skales Visuals, the engine that needs no key, and it is always there. Google and Replicate sit alongside it and appear as choices once you have connected a key for them. Studio opens on Skales Visuals the same way.

- **Your MCP tools answer plain requests.** Tools from a connected MCP server have always run without an `@` mention, but Skales used to suggest reaching for the picker to use them. It now treats them like its own tools on any matching request, and the `@mcp-` picker is for attaching a server's resources or prompts as extra context.

- **Skales reaches into your knowledge base on its own.** The documents you add to the Knowledge Base were searchable with the `/rag` command; Skales now reaches for them by itself too. When you ask about something from a file or note you put in, it searches your indexed documents and answers from them instead of guessing, and it offers this only once you have added a document.

- **Summarizing a link gives you a readable page.** Ask Skales to summarize a web address and the result now comes back as a small rendered page instead of plain text, which reads better for an article. Summarizing text you typed stays plain, and your own choice in the style bar always wins.

- **The chat box highlights commands and mentions as you type.** A recognized command like `/goal` and an `@` mention of a skill or server light up as you write, and `/goal` now works wherever you drop it in a longer message, not only at the start.

- **The window frame follows your theme.** On macOS and Windows the title bar now takes on the active theme instead of staying the plain system frame, so the whole window matches the rest of Skales. Linux keeps its native frame.

- **Skales opens faster.** Start-up no longer waits on background setup before the first screen shows, the first-run check runs once instead of on every move between pages, and a page you are about to open from the sidebar loads a step ahead when you hover it.

- **Subscription and bring-your-own-key models use their full context window.** A ChatGPT, Claude, or Gemini sign-in, and a pasted token, is now sized to the real window of the model behind it, so a long chat no longer starts compressing far too early.

- **A passing connection test means a working connection.** Testing a custom provider endpoint now builds its address the same way a real chat does, so a full or versioned URL that works in conversation no longer comes back as a failed test.

- **Skales loads your installed skills only when they are needed.** If you had many skills installed, the full instructions for every one of them were packed into each message, which slowed responses and could make Skales talk about skills it was not actually using. It now keeps a short list of your skills and pulls a skill's full instructions the moment it is relevant or it decides to use one, so responses stay fast and accurate no matter how many skills you have.

- **Long runs keep their thread instead of running out of room.** When a task takes many steps and the conversation grows large, Skales used to quietly drop the oldest turns and could lose track of what it had already done. It now compresses the older turns into a compact progress note and keeps working from it. On the models that support it the compression happens on the provider's side; everywhere else Skales does it itself, so a long task stays on course.

- **Skales changes tack when it gets stuck on a goal.** While working a goal, if it catches itself repeating the same step or going several rounds without real progress, it now stops, rethinks, and tries a different approach instead of grinding through its step budget on something that is not working.

- **Goal planning can run on a cheaper or local model.** The background steps that plan a goal, check its progress, and sum up where it is can now be pointed at a smaller or local model while your main model does the real work, so a long-running goal costs less to keep going. It uses your main model unless you choose another in the goal settings, so this stays your choice and works with any provider.

- **Playground builds apps more reliably on a local model.** A smaller or self-hosted model now has a little more room on length and the number of AI calls so its apps make it through, and an app that calls a function it never wrote is caught and rebuilt instead of shipping broken. The prompt also hands the model a full page to fill in, so weaker models start from solid structure.

### Fixed

- **Group chats and Organization runs start again.** Kicking off a multi-model group chat or running an Organization could fail to begin; both now start and run the way you set them up.

- **Discover works on a phone, and keeps your tag.** On a narrow screen the Spaces and details panels now open over the feed instead of sliding behind the sidebar, so everything is reachable. And if you already have a Discover tag, Skales keeps it instead of asking you to pick a new one after an update.

- **Discover keeps what you share.** Shared skills, templates, and images used to scroll out of the feed and vanish for good once it filled up, which made Discover feel like a feed that resets. They now stay permanently; only everyday chatter ages out.

- **The Brand Kit reaches Studio.** Turning on Brand Kit in an image or video generator now actually applies your brand: your palette in the prompt, plus your logo for Skales Visuals video. Before, the toggle recorded your choice but changed nothing.

- **You can reset what Skales learned about you.** Settings, Memory, Adaptive personalization now has a Reset that erases your agent's character and everything Skales has picked up about how you work, in one click.

- **AIPointer keeps the look you gave it.** AIPointer's own light or dark setting no longer follows the desktop theme; it stays the way you set it whatever theme Skales is on.

- **Opening a finished chat from a notification lands on that chat.** When a reply finished while you were on another page, the notification and the sidebar dot used to drop you on the New Chat screen instead of the conversation that answered. Both now reopen the exact chat that finished.

- **The New Chat screen lines up and opens its commands.** On the opening New Chat screen the "+" button and the send button now sit centered on the input row instead of dropping below it, and typing "/" or pressing the commands button opens the same command list the in-chat composer has.

- **The screen-share picker sits where it should.** Choosing a window to share opened a panel pinned against the sidebar with empty space beside it. It now centers on the screen the way it was meant to.

- **Clicking Chat lands on New Chat.** With a goal parked in your last session, clicking Chat used to reopen that session instead of the New Chat screen. It now opens New Chat unless a chat is actively replying, and a parked goal waits for you in your recent chats and on its card.

- **A shared video plays as a video.** When Skales shared a YouTube link it could come through as a broken player. It now plays as a clean embedded video, while a richer page that happens to include a video keeps its full layout.

- **The completion sound only plays when you are away.** A finished reply used to chime even while you sat on the chat waiting for it. The sound now plays only when Skales is in the background or you are on another page; the toast and the sidebar dot still appear either way.

- **Links show a preview card.** A web address you or Skales drop into the chat now shows a small preview with the page title and image, for both sides of the conversation.

- **A capped local model is told only the tools it kept.** When you limit how many tools a local model may use, Skales used to still describe whole groups of tools that did not fit the limit, so the model would reach for one it never received and report it could not help. The prompt now lists only the tools that made the cut, so the model works with what it actually has.

- **Messages stay put when several arrive at the same time.** If two were saved in the same moment, a Friend Mode check-in landing while you replied on Telegram, or a Desktop Buddy reply arriving alongside one in the main window, one of them could quietly overwrite the other and vanish from your history. Each message is now saved in a way that cannot overwrite another, so they all stay where you left them no matter how they overlap.

- **Deleting your message now clears its reply too.** Deleting one of your own messages used to leave the response it produced sitting right underneath it, even though the confirmation said the responses below would go too. Deleting your message now removes the whole exchange, the reply and any tool results that came with it, exactly as the prompt promises.

- **Skales recognizes what it is set up to do.** With a provider configured for something like image generation or web search, Skales could still talk about that feature as if it were missing or not set up. It now reads what is available from the same place your provider keys live, so its sense of what it can do matches what you have configured.

- **The Compact button shortens a long chat when you press it.** Compacting on demand reused the limits meant for the automatic version, so the button did nothing until a chat was already very long. Pressing it now shortens the conversation whenever you ask, and it stays disabled only while Skales is mid-reply.

- **Large files and long results no longer get cut off and lost.** When Skales read a big file or ran a command with a lot of output, only the first few thousand characters reached it and the rest was dropped, so it could act on half a picture. It now reads large files in pages and, when a result is too big to show at once, saves the full text and keeps reading from where it left off. It also sizes how much it keeps to the room left in the conversation, so a single big result can no longer crowd out everything else.

### Security

- **Your saved provider API keys are now stored encrypted.** They used to sit in plain text in your settings file. Skales now keeps them scrambled on disk and unlocks them only when it needs to reach a provider. Keys you already saved are converted automatically the next time you open the app, with nothing to re-enter. Tokens for connected services like Telegram or Slack are not encrypted yet.

- **Sign-in and link previews are hardened.** The sign-in window now treats whatever a provider sends back as text rather than markup and only accepts messages from Skales itself, closing a way a crafted error could slip content into the page. Link previews and the Studio read-from-web feature now refuse web addresses that point back at your own machine or your local network, except where a design served locally is the legitimate target.

## v10.4.5

Reliability fixes for the messaging channels, Friend Mode, and the chat view.

### Fixed

- **Skales's own check-ins stay out of your open chat.** When Skales reached out on its own, a Friend Mode note or a quiet identity check-in, the message could land in whatever chat you had open on the desktop and take it over. Those check-ins now keep to their own place, so the chat you are working in stays yours.

- **WhatsApp conversations stay in their own chat.** Messages from WhatsApp used to drop into whichever chat happened to be open on the desktop. WhatsApp now keeps its own dedicated Skales chat, the same way Telegram does, so a conversation you start there stays in one place.

- **Friend Mode keeps the thread when you switch screens.** When you moved from working on the desktop to a message from Skales on Telegram or WhatsApp, its note could arrive out of context, more like a generic notification than a friend picking up where you left off. Friend Mode now reads what you were just doing across all your channels, so its message stays on topic and in your voice, while still only ever writing to the channel it reached you on.

- **Formatting comes through on Telegram.** Bold text, lists, and code blocks used to show up as raw symbols and stray asterisks in Telegram replies. They now render properly, and code stays in a clean block you can copy.

- **Replies stop disappearing.** Some replies lived only on screen and vanished the moment you sent your next message or reloaded the chat. This included the playful easter-egg replies, the hello greeting, in-chat search results, the project commands, knowledge-base lookups, persona and model switches, and generated images and videos. All of these now stay in your chat history where you left them.

- **Short messages get a proper bubble.** A very short message, yours or a short reply from Skales, drew a bubble too narrow to fit the copy and other action buttons. Short messages now keep a comfortable minimum width, so those buttons always fit.

## v10.4.1

Reliability and clarity fixes for Codework, local models, file access, and the chat view.

### Fixed

- **Codework tells you when a local model can't carry out a task.** With some local models, Codework could end a task as if it had succeeded while nothing was actually built or changed, or run on until it stopped on a confusing error. It now keeps the model pointed at doing the work for real, and when it still can't, it ends with a clear message and what to try next, instead of a quiet success that left nothing behind.

- **File-access settings are clearer and fail early.** Folders you add under Custom are checked the moment you add them, so a missing, read-only, or otherwise invalid path is flagged with a warning instead of breaking mid-task. When file access is set to Workspace Only and something tries to use a path outside the workspace, Skales now stops with a clear message about how to allow it, instead of quietly saving the file somewhere you did not expect. The panel also notes that these settings cover file access only, which is separate from Computer Use control of your screen, mouse, and keyboard.

- **Local models get the time they need to respond.** The previous limit could cut a local model off while it was still loading into memory or working through a longer step, which surfaced as a confusing timeout. Timeouts now start higher for local and self-hosted models, and the per-provider timeout you set in Settings is honored everywhere Codework runs, not only in chat.

- **Multi-step tool runs read as one block.** When Codework takes several tool steps before answering, for example reading a file, listing a folder, then replying, the steps now collapse into a single section with one tool-results view instead of stacking as separate windows.

- **Copy your own messages.** The copy button now appears on your messages too, matching the one already on Skales replies.

## v10.4.0

AIPointer ⦿ is now a built-in part of Skales.

AIPointer is a cursor-anchored quick-ask AI overlay by the same team behind Skales. Hold a key, ask a question over whatever your cursor is pointing at, get an answer. Standalone AIPointer is a fast 2-second loop. The version inside Skales is an upgrade: it already knows who you are, what you have been working on, and can write straight into your Skales todos, calendar, notes, and memory. Heavy work hands off to the full Skales chat with one click.

### Added

- **AIPointer ⦿ integration.** Enable in Settings > Appearance > AIPointer ⦿. Hold the right Cmd key (right Ctrl on Windows / Linux) or wiggle your cursor, and a translucent quick-ask box appears over whatever app you are in. Type or speak a question. Get a fast answer that respects your timezone, language, and current projects without having to re-introduce yourself every time.

- **AIPointer knows you, because Skales knows you.** The overlay reads your Skales identity (name, language, timezone, active projects, interests) on every query. It pulls in recent topics when you ask "what was I working on?", and looks up entities from your knowledge graph when you mention names like "Alice" or "the Vienna trip". Nothing to type twice, nothing to set up.

- **AIPointer can do things in Skales.** Four atomic actions fit the 2-second loop. Say "remember this" and it goes to your long-term memory. Say "add to my todos" and it lands on your task list. Say "save as a note" and it appends to your dashboard note widget. Say "schedule lunch tomorrow at noon" and it captures the calendar intent. No app-switching for quick captures.

- **Send to chat.** Any AIPointer response carries a Send button that hands the query and the answer off to Skales main chat as a new session. Use it when the quick loop is not enough, like codework, browser automation, or anything multi-step. The imported session has full context, ready to continue with the complete Skales toolset.

- **Whisper and Kokoro voice engines.** New in Settings > Integrations > Voice > Local AI Voice Engines. The Kokoro text-to-speech runtime is live and powers AIPointer ⦿ read-aloud in 28 voices, fully on-device, no API key, no cloud round-trip, no per-minute cost. The Whisper speech-to-text model downloads here too, but the on-device runtime arrives with the next AIPointer sync; until then the AIPointer mic transparently falls back to your Skales transcription cascade (Groq / OpenAI Whisper API / Azure / local STT URL, pick yours under Settings > Voice). Skales main chat keeps its own TTS + STT cascade in v10.4.0; sharing Whisper + Kokoro across both surfaces lands in Skales v11.

- **Rich response formatting when it earns its keep.** For structured answers like comparisons, multi-step how-tos, planning, or summaries with metrics, AIPointer renders the response with cards, callouts, numbered steps, and metric tiles. Plain questions stay plain prose so a "what time is it for me?" answers in one line. Skales chat renders the same rich layout when you Send to chat, so the look survives the hand-off.

- **Discover indicator (opt-in).** Show others on Discover that you are using AIPointer ⦿ right now. Anonymous, just your Discover tag, no query content shared. Off by default. Toggle under Settings > AIPointer ⦿ > Discover.

- **AIPointer Settings tab.** A dedicated panel under Settings with every AIPointer knob in one place: trigger hotkey, mouse wiggle, hold duration, voice-first mode, chat-only mode, light or dark theme, accent colour, cursor halo intensity, screenshot crop size, pill offset, auto-approve tools, session transcripts, plus a Skales Integration section for the "activate only when Skales is minimised" behaviour. The current AIPointer version (and the fact that it has its own release cadence) is displayed at the top.

- **Built-in spell check.** Every editable surface across Skales, from the chat input to Settings to titles, now shows red underlines on misspelled words. Right-click a flagged word for suggestions and Add to Dictionary. A toggle in Settings turns it off, and you can pick spell-check languages independently of your UI language.

- **Sign in with your ChatGPT subscription.** Skales now supports OAuth sign-in for ChatGPT Plus, Pro, Business, and Enterprise accounts. Pick the new Subscriptions card at the top of AI Providers, click Sign in, and Skales routes chat through your existing ChatGPT plan with no API key required. Models available through your subscription show up in the model picker automatically.

- **Bring your own Claude or Gemini subscription token (advanced).** Power users who already have a token from Claude Code CLI or Gemini CLI can paste it into Skales under Subscriptions. The feature is opt-in, behind a disclaimer you actively accept. Skales sends the request unchanged, no header spoofing, no client masking. Anthropic and Google prohibit this in their consumer terms and have suspended accounts that route subscription tokens through third-party tools. The choice and the risk are yours. Sanctioned alternatives (Anthropic Console, Google AI Studio) sit one click away on the same cards.

- **Send your location to the Telegram bot.** Share a location or a place through Telegram's attachment menu and Skales now reads the coordinates. Ask "what is the closest bakery" and it can answer from where you are, instead of asking you to type an address or paste a map link.

- **Pick which agent answers on Telegram.** A new /agent command in the bot lets you switch between Default Skales and any of your custom or built-in agents. The chosen agent replies with its own instructions, model, and tool scope, and keeps its own Telegram chat history separate from the rest. Default stays Skales until you switch.

### Known limitations

- **Subscription tokens were not yet encrypted at rest in this release.** Sign-in tokens stayed on your own machine and never left it except to the vendor the token belongs to, but they were not encrypted on disk. Later versions of Skales encrypt them with your operating system's own protected storage.

### Changed

- **The Spotlight Bar has been retired.** AIPointer covers the same quick-ask use case with cursor anchoring, voice, vision, and richer affordances. The old Spotlight hotkey no longer triggers anything. Enable AIPointer in Settings > Appearance.

- **Saving a provider key actually tests it.** Pasting an API key and clicking Test now also enables the provider when the test succeeds and, if your active provider is still the empty install default, switches to the one you just configured. Already-active choices are never overridden.

- **OpenAI provider passes Organization on outgoing requests.** ProviderConfig carries an optional organization field; when set, every outgoing OpenAI call attaches the OpenAI-Organization header. Project-scoped (sk-proj-) and service-account (sk-svcacct-) keys that depend on an org binding now resolve correctly. The OpenAI provider card shows an inline hint when it detects a sk-proj- or sk-svcacct- key, and exposes the Organization input right below the API key field.

- **Friend Mode is back, and it remembers the conversation.** Skales can reach out to you on its own again, by Telegram, WhatsApp, or email. Each note is now built from your most recent chat and the things you have talked about before, so it reads like a friend picking up the thread instead of a template. It speaks in the same voice as your chat assistant: keep things formal and it stays formal, chat with emojis and it keeps using them. Ask it to drop a subject and it stays dropped, even across messages. It also picks its moments: it holds off while you are mid-task and when your last note has gone unanswered, rather than arriving on a fixed clock. Switch it on under Settings > Friend Mode, and use Test Friend Mode to confirm your channels.

- **Pick your web search engine.** Web search is no longer Tavily-or-nothing. A Search provider dropdown under Settings > Integrations > Web Search lets you choose DuckDuckGo (no API key, the new default), Tavily, Brave, a self-hosted SearXNG instance, or hand web search to a connected MCP server. Skales always gives the agent one search tool and swaps the engine behind it, so you no longer have to wire up an MCP server just for basic web search.

- **Run several custom providers at once.** You can register multiple OpenAI-compatible endpoints side by side (a local llama-swap, a vLLM box, a gateway, a second LM Studio) under Settings > AI Providers > Additional Custom Providers, each with its own name, URL, key, and model. They now resolve correctly when selected and appear in the chat model picker, so you can switch between them per chat instead of rewriting one shared slot.

- **Projects has one clear action button.** "Discuss with AI" and "Start working" did almost the same thing, which was confusing, so "Start working" is gone. "Discuss with AI" opens the project chat the same as before.

### Fixed

- **The agent uses your MCP search tool when Tavily is off.** With no Tavily key, Skales still told the model it had built-in web search, so the model reached for a tool that was not there and waved off a connected MCP search provider like DuckDuckGo as something it could not call. Now, when Tavily is off, the prompt stops advertising the built-in search, and the agent treats your connected MCP search tool as the real web search.

- **Skales keeps your connected MCP tools in reach.** Tools from any MCP server you connect now stay available to the assistant from one message to the next, whatever you named the server, and including on local models where the built-in tools used to crowd them out. While a server is still connecting or briefly drops, Skales no longer claims those tools are ready when they are not, so you stop getting confident answers about tools that cannot actually run.

- **No more phantom GitHub setup.** Skales stopped describing a built-in GitHub integration it no longer ships. GitHub is reached by adding the GitHub MCP server under Settings > MCP, and that is the only place Skales points you to now.

- **Clearer Google Calendar setup.** The Calendar settings now spell out what actually connects your calendar. An API key only reads public calendars (your own calendar returns a 404 with a key), so personal read and write needs OAuth. The OAuth section now calls out the step that trips most people up: a Google consent screen left on "Testing" blocks sign-in until you add yourself as a test user or publish the app.

- **Outlook Calendar connects again.** Connecting Outlook failed right after the Microsoft app setup because Skales asked for a sign-in redirect Microsoft no longer accepts. It now uses the supported desktop sign-in, works with single-tenant work accounts (enter your Tenant ID), and needs no client secret. The card also gained the Save and Test buttons the Google and Apple cards have, remembers your details across reloads, and the setup box spells out exactly what to register in your Microsoft app. Once connected, your Outlook events show up in the Planner alongside Google and Apple, the same as before.

- **Vision skill recognises local providers.** With a local Ollama or Custom Vision Provider configured, the Vision skill no longer stays stuck on "enabled, needs setup". The screenshot tool is callable as soon as you have a vision model selected. No API key required for local endpoints.

- **Clearer OpenAI errors.** Invalid key (401), exceeded quota (403), missing model access (404), and rate limit (429) each surface as their own toast with the right next step, instead of all collapsing into a single "API key failed".

- **Local models run your tools again, instead of just describing them.** On Ollama and other local or custom models, the agent would sometimes write out a tool call as plain text and then stop, so Codework and Chat produced a detailed plan but changed no files and ran no commands. Skales now recognises a tool call written that way and runs it, the same as it does for the big cloud models, so work that needs real edits, commands, or searches actually goes through on local models.

- **Skales stops claiming skills it has not been set up for.** When a skill was switched on but had nothing connected yet (say WhatsApp or Discord with no account linked), the model could still announce it was able to send messages there. Skales now states plainly which skills are ready to use and which still need setup, and it no longer lists the actions of a skill that is not ready, so it stops making promises it cannot keep.

- **The Filesystem MCP server starts on Windows.** Its default folder was set to a path that only exists on Mac and Linux, so on Windows it failed every time it tried to start. Skales now points it at the right temporary folder for your system, and quietly repairs a server that was already saved with the old path, so it connects instead of looping on the same error.

- **The MCP server list shows the real state.** Under Settings > MCP a server that was switched on always showed a green dot, even when it was failing to connect. The dot now reflects what actually happened on the last attempt and shows the error next to a server that could not start, so a broken server no longer looks healthy.

- **Provider fallback skips the entries it cannot use.** If you turned on provider fallback and the chain listed a cloud provider with no key, Skales tried each empty one and failed down the list every turn before reaching a working local model, which looked like it was switching models at random. It now skips a fallback provider that has no key or sign-in and goes straight to one that can actually answer.

- **Apps you build in Playground stop breaking on a missing helper.** Smaller models building a Space sometimes left out one of the small helper functions while still calling it, so the finished app showed an error like "mdToHtml is not defined" the moment it opened. Skales now provides those helpers to every generated app itself, so the app runs even when the model forgets to include one.

- **The left sidebar shows every item again.** With a lot of features switched on, the navigation list could grow tall enough to push the bottom actions (Settings, Stop Server) off the screen. The list now scrolls on its own and the bottom actions stay pinned and reachable, in every theme.

- **Telegram messages stay in their own chat.** Messages you send to the Telegram bot used to land in whichever agent chat happened to be open on the desktop, and then show up a second time in your default chat. The bot now keeps its own dedicated Skales chat, so a conversation you start on Telegram stays in one place and stops appearing twice.

- **The voice button appears once speech-to-text is set up.** The microphone in chat stayed hidden unless you switched on the Voice Chat skill, so setting up a Whisper server or a speech-to-text key looked like it did nothing. The mic now shows up as soon as any speech-to-text option is in place (your own local Whisper server, Groq or OpenAI Whisper, or Azure), and the Settings hint now says the URL field is what powers it. An always-on wake word is not in this release.

- **/clear in Telegram actually starts a fresh chat now.** The command used to reply "Context cleared" without doing anything, so the bot kept right on with the old conversation. It now opens a new chat for whichever agent you are talking to (/new does the same), while the previous conversation stays saved in your history.

- **The dashboard Tasks widget shows the real list.** It loaded once and then held on to tasks you had already deleted, and hid finished ones entirely. It now refreshes on its own, puts active tasks first with older ones below in a scrollable list, and a task you delete (or clearing them all) drops off right away.

- **No more double "+" on the dashboard Connections card.** "Manage connections" showed two plus signs and dropped you at the top of Settings. It is now one clean link that opens the AI Providers tab directly.

- **Remove an MCP environment variable while setting one up.** Each variable row in the MCP setup got a delete button, so a typo in a variable name no longer means closing Settings and starting over.

- **The Identity Maintenance task is tidy again.** On the Tasks page it had started showing its internal setup text and a delete button that never did anything. It now shows a short note instead: it lives under Settings > Memory, where you switch it on or off, and you can still run it by hand from the Tasks page. Long task descriptions also collapse behind a Show more toggle so the list stays readable.

- **AIPointer ⦿ actually sends the screenshot now.** The overlay collected your question but never captured or attached the cursor-area screenshot, so vision-capable models kept replying "I can't see your screen". AIPointer now grabs the screenshot (and your clipboard, when that chip is on) at submit time and sends it along, so "what am I looking at?" works on wiggle, hotkey, and bullet-click triggers.

- **AIPointer ⦿ region selection captures the right area.** Holding the trigger a second time and dragging a rectangle now attaches exactly that crop to your next question. Previously the drag drew the box and flashed "Selection captured", but the captured pixels were thrown away and the query silently fell back to the default cursor-centered shot. The drawn region now wins over the default capture and over the chat-only chip, and it clears itself after one query (or when you dismiss the box) so a leftover crop never sticks to a later question.

- **AIPointer ⦿ honours your dedicated Vision Provider.** Earlier the overlay sent every screenshot through whichever AI provider was active for chat, ignoring the separate Vision Provider you set up under Settings > AI Providers. Now AIPointer reads that config the same way the main chat's screenshot tool does: bring your own local LLaVA on Ollama or a custom localhost endpoint for vision, and AIPointer routes the cursor screenshot straight there while text questions keep going to your usual chat brain. Single-image queries only; multi-image is unchanged and stays on the chat brain.

- **AIPointer ⦿ auto-attach finally fires inside Skales.** Highlight up to 5 files in Finder (macOS) or Explorer (Windows), press the AIPointer hotkey, and the selected files queue for the next query. The paperclip button next to the mic also opens a file picker. The "N files attached" pill above the prompt confirms what's lined up; the queue clears after each submit. Images become vision context, text files inline into the prompt, binaries (PDF, DOCX, XLSX) get referenced by name. macOS will ask for Finder Automation permission on first use.

- **Bullet pill stops flickering while you type.** Typing in any text field outside Skales (browser address bar, another app's composer, the chat editor itself) no longer makes the small status pill at the bottom of the screen flicker on every keystroke. The internal "user is busy elsewhere" signal now only runs when the big AIPointer answer pill is actually open, so the idle bullet stays calm.

- **AIPointer ⦿ wiggle / hotkey stop dying after one missed trigger.** When the "activate only when Skales is minimized" guard rejected a trigger (because Skales was the focused window), the trigger state machine stayed stuck pretending an AI session was open. Subsequent wiggles or hotkey holds got silently swallowed until you clicked the bullet manually to force a reset. The guard now rolls the state back cleanly so the next trigger gesture works straight away.

- **No more dark overlay across the screen on minimize.** AIPointer's transparent overlay window paints transparent regardless of which Skales theme is active. The wake-up recovery effect that re-applies Skales' theme background was firing inside the overlay's renderer on every visibility change and stamping a solid dark background over the user's screen. That effect is now scoped to the main Skales window only.

- **AIPointer ⦿ response text readable in light theme.** A hardcoded white text colour on the overlay wrapper was overriding the response markdown's per-theme colour rules. Removed, so the answer body and headings follow your AIPointer Appearance picker (Light / Dark) the way the rest of the surface already does.

- **Spell-check actually underlines misspellings in the chat composer.** The chat input had `spellCheck={false}` hardcoded on both the mobile and desktop composers, which silently nullified the Settings > General > Spell-check language list. With the toggle removed, your selected dictionaries (English, German, Croatian, the rest of the 13 supported languages) get red underlines and right-click suggestions like every other editable field. The same fix lands on the AIPointer ⦿ prompt input.

- **AIPointer ⦿ Settings tab readability.** The FAQ section's titles were rendering invisible on dark Skales themes (white text on the tab's forced light surface). Replaced the theme-variable colours with the tab's own dark-gray palette so every entry, Skales-specific ones at the top and the longer AIPointer reference list, stays readable in any theme, all behind one consistent accordion. The "What AIPointer is / is not" block above it and the "Coming with Skales v11" preview switched to the same palette.

- **AIPointer ⦿ tab header.** The opener box now renders "AIPointer" as one word the way it's branded. The duplicate header card with "Cursor-anchored quick-ask..." was redundant and is gone; the brand opener at the top of the tab and the Help card at the bottom keep the GitHub link and version.

- **AIPointer ⦿ inside Skales no longer asks the system keychain on launch.** The standalone AIPointer keeps its own encrypted provider key store; the in-Skales build doesn't need it because Skales bridges every provider call. On launch the AIPointer module now detects it's running inside Skales (presence of the Skales data directory) and skips the keychain probe entirely, so users with an existing standalone AIPointer install don't see a "Skales would like to use the keychain" prompt every time they open Skales.

## v10.3.7

Settings performance pass, a Friend Mode safety hold, GitHub moves to MCP, plus a handful of cleanups.

### Changed

- **Settings opens faster.** Switching providers, opening the model picker, and changing tabs feel responsive again. A deeper rework that makes every Settings tab open instantly is queued for the next release.

- **Settings reachable from every menu.** A Settings shortcut now sits at the bottom of the sidebar, the icon rail, the top navigation, and the mobile menu, right above Stop Server.

- **GitHub access moves to MCP.** The built-in GitHub setup in Settings has been removed. GitHub stays reachable by adding the GitHub MCP server under Settings > MCP.

- **Friend Mode temporarily off.** Proactive outreach is paused while the underlying session work it depends on is finished. The feature returns in an upcoming release.

- **MCP page retired.** Adding, editing, testing, and removing MCP servers all happen in Settings > MCP. The standalone page that duplicated this surface is gone.

- **More accurate provider filter.** With "Show only active" enabled, the provider list only shows the providers you have actually configured.

- **Clearer import message.** When a chat history import finishes, the confirmation now points to your History panel and how to find the imported conversations.

### Fixed

- **Playground stops failing on dashboards and analyzers.** Generating a dashboard or analyzer no longer trips a validation check meant only for research.

- **Icon rail menu stays on screen.** The overflow menu on the left rail now opens upward instead of running off the bottom of the screen on shorter windows.

- **Sidebar buttons stop getting clipped.** Buttons that used to disappear behind the window edge on smaller heights are now visible.

- **Discover posts keep their line breaks.** Multi-paragraph posts render with the paragraph breaks the author wrote, instead of collapsing to one block of text.

- **Filesystem connector works on Windows.** Adding the built-in filesystem connector no longer fails on Windows out of the box.

- **Broken connectors stop being retried every turn.** Failing connectors back off into a cooldown, and that cooldown survives an app restart instead of resetting on every launch.

- **Identity Maintenance writes timeless profile content.** The daily profile update no longer bakes the running Skales version or release dates into your saved profile, and rewrites any sentence that still carries one.

- **Identity Maintenance no longer shows up in chat.** The internal task body for the daily maintenance run is no longer visible in your chat stream when the schedule fires.

- **Identity Maintenance scheduled task no longer duplicates.** Repeated runs of the setup flow only ever leave one entry in place.

- **Profile better at parsing pasted paths.** Pasting a path or URL into chat no longer mangles it into a garbage token in your saved profile.

## v10.3.5

Background work, a lighter prompt on smaller models, a one-click memory mode, and a round of fixes.

### Added

- **Background tasks.** You can start something, switch to another view, or close and reopen the window, and it keeps running. When you come back, the result is waiting for you.

- **Tasks stay paused only when they should.** A run pauses only when it genuinely needs your approval, not just because you navigated away. Approval requests survive navigation, so nothing gets stranded or lost.

- **Get notified when work finishes.** When a background task completes while you are on another page, you get a notification with sound and a marker on the chat in the sidebar.

- **Memory mode in one click.** The memory mode button in the chat header now cycles through all three modes with a single click, each with its own clear colour, so you can adjust it without opening settings.

### Changed

- **Images you send stay visible.** An attached image now shows as a thumbnail in your message and stays there, even after a reload. Click to open the full image.

- **Leaner prompt on smaller models.** The prompt sent to smaller and local models is lighter, without losing any awareness of what Skales can do.

### Fixed

- **Smoother scrolling when switching between conversations.** Opening one conversation out of another no longer overshoots before settling.

- **Imported chats are clearly marked by source.** Chats migrated from another tool now carry a badge in your history naming where they came from.

- **Stop and killswitch respond immediately.** Both fire instantly, even in the middle of a running task.

## v10.3.4

Hotfix session.

### Fixed

- **macOS Apple Silicon installer launched into a "Server files missing, please reinstall Skales" error.** The v10.3.3 arm64 build was packaged incorrectly. Both Mac architectures are now packed from a single shared build pass that cannot diverge.

- **Left sidebar "More" menu rendered empty on smaller windows (most visibly on Windows).** Clicking the three dots opened a popover whose items were not visible. The menu now renders as intended.

## v10.3.3

A focused hotfix release. The MCP-button fix from v10.3.1 and v10.3.2 finally lands on every setup. Assistant replies no longer appear trapped inside the Tool Results disclosure. Local models with large context windows stop feeling reset after a few tool-heavy turns. And Discover gets a focused upgrade.

### Fixed

- **Edit a configured MCP server straight from Settings.** Each server row in Settings now has an explicit Edit button next to Test, Toggle, and Remove. Clicking it opens a form pre-filled with the server's current settings. Every row icon carries a tooltip and screen-reader label, translated across all 12 locales.

- **MCP status badge updates immediately after a successful Test.** A green Test result no longer leaves the badge stuck on "stopped". It flips to "connected (N tools)" right away.

- **Environment variable values are readable by default.** They used to render as password dots, which made copying tokens out of a vault painful. They are now plain text with a per-row Show / Hide toggle for screen-sharing situations.

- **Assistant prose no longer hides inside the Tool Results disclosure.** When an assistant turn had both an answer and tool calls, the answer used to render inside the collapsed disclosure, so reading the reply required clicking the header first. The prose renders above the disclosure now.

- **Local endpoints with large context windows stop feeling reset after a few tool-heavy turns.** When Skales had no live context-length info for a model, it used to clamp it to a conservative value, which triggered auto-compression far too aggressively. The floor is higher now, and a wider range of community model families are recognised so they default to their real context size. User overrides under Settings > Override Model Limits still win on top.

- **Override Model Limits respects every Custom Provider slot.** Users running multiple Custom Provider slots can set one override that applies to all of them, unless an explicit per-slot row exists. Capitalisation differences between your override row and what the server reports no longer silently drop the value.

- **Skales can read its own past conversations now.** When you ask the assistant in natural language "what did we discuss about X yesterday?" or "find that chat where I planned the Vienna trip", it can now pull snippets directly from your saved sessions (including chats imported from ChatGPT, Claude, and others) and answer instead of falling back to memory only. The /search slash command is unchanged. It stays a fast local text scan.

- **Ollama "Max tools" slider labels line up with the slider value.** Setting the slider to 25 used to land it between two label positions that pretended to be "15" and "35" but were actually placed elsewhere. Labels now sit at their true values. Visual only, behaviour unchanged.

- **Web reader returns the full extracted page.** More of the extracted page reaches the model now, so summarising a long article works the way the tool description promised.

- **The /projects autocomplete shows a real description.** Typing /p used to surface the raw translation key in the suggestion. Translated across every language.

### Added

- **Recent group in the chat-header model picker.** The picker leads with a small "Recent" section showing the last five models you actually sent with, ahead of any curated list. Locally installed Ollama models and custom model ids land at the top of the dropdown without needing to be in any built-in catalog. A Clear button empties the list.

### Discover gets a focused upgrade

**Privacy disclosure with explicit consent.** The Join Discover wizard now shows a small paragraph under the tag input explaining exactly what is stored on the Skales server (the tag you chose and a one-time random ID, not your name, email, or device fingerprint), and a checkbox you have to tick before the Next button enables. The shorter privacy line on the final step has been rewritten to match what is actually stored, not the vague "no personal data" copy from before.

**Polls.** Posts marked as polls render with a question and up to eight option buttons. Click an option to vote. One vote per identity per poll. Expired polls render as Closed and the buttons disable.

**Rename your tag, or reset your identity entirely.** Settings > Discover gets two new buttons next to Edit Profile and Leave Discover. Rename Tag changes your gamertag in place, your existing posts and votes stay attached. Reset Identity wipes both the local copy and the server-side row, so you can rejoin under a fresh name.

**Activity tab.** A new chip in the Discover filter row opens an inbox grouped into Mentions, Replies, and From Admin. Auto-refreshes every minute while open. Click an unread item to mark it read.

**Hashtags.** Inline #tags in any post are clickable now. Click one to filter the feed to posts that carry that tag. The filter banner at the top has a Clear button. Works alongside the existing author filter.

**Pinned-by-admin posts.** Posts the Skales admin flags as pinned sort to the top of the feed with a small Pinned badge.

**Force-rebrand handling.** If the Skales admin flags your tag (impersonation, offensive content, etc.), Skales wipes your local identity, shows a toast, and routes you back to onboarding so you pick a new tag. Your existing posts and votes are not deleted, only the tag is reset.

**Smaller Discover polish.** Author avatars get a soft pulse when the post is fresh. The empty state offers three quick-jump buttons instead of the dry "Be the first to post!" line. The compose box placeholder cycles through short prompts when empty so the box never feels stale. Translated across all 12 locales.

### Smoke-test follow-ups

- **Gemini 2.0 family removed.** The 2.0-flash family was deprecated upstream. Picker entries replaced with 2.5-flash, 2.5-pro, and the 3-preview models. Existing settings that point at 2.0-flash auto-migrate forward on next load.

- **In-app navigation no longer reloads the page.** Clicking Open Settings on a toast, the Memory Mode badge in the chat header, and the /settings, /discover, /studio, /codework, /wordpress slash commands all navigate inside the app.

### Deferred

Thread modal (click a reply quote to see the full thread), user profile modal (click a tag to see bio plus recent posts), and post scheduling are not in this release.

## v10.3.2

A maintenance release. Things that already worked now work the way the screens promised. Eleven tracked bugs squashed across MCP, providers, importer, install docs, and locales.

### Fixed

- **Adding an MCP environment variable works on every platform.** The "+ Add environment variable" trigger used a prompt that was unreliable. Replaced with an inline text input and confirm button. Enter-key confirms.
- **MCP Edit deep-link lands on the right tab and scrolls to the right form.** Clicking Edit on the MCP page could previously land on the wrong tab or scroll to an unrelated row. Reliable now. The same fix flows through to template and WordPress / auto-backup anchors.
- **Ollama "detected" banner no longer contradicts a red "not running" hint on Win11.** Previously, a green banner and a red error box could show at the same time when Ollama was running but had no models. The banner now splits: green "Ollama detected, N models available" when models are present, amber "Ollama detected, no models found. Run: ollama pull llama3.2" when zero models are present.
- **Imported ChatGPT, Claude, OpenClaw, and Hermes conversations show up in the sidebar.** Imported chats were previously invisible in the Sessions sidebar. Each imported conversation is now a real chat session with a `[Imported: <source>]` title prefix. Memory search across imported conversations finds substance now.
- **Same-provider retry on transient errors.** Transient failures (network resets, timeouts, HTTP 502, 503, 504) no longer go straight to "No response received." in the chat UI. Skales now retries on the same provider before bubbling up. Layered above the existing model-fallback and cross-provider fallback. Configurable per provider under Settings > Providers.
- **Clearer error messages when using a meta-router.** When a meta-router is configured through the custom-provider slot and an upstream provider is not authenticated, the router used to return a generic network error. Skales now rewrites the message to "Router could not reach upstream provider X. Open your router dashboard and finish connecting X, or switch to a model from a connected provider."
- **API key redaction in error messages covers more providers.** Previously caught Anthropic and OpenAI key forms. Now also catches xAI, Groq, Google, JWT-style keys, and credentials embedded in proxy URLs. Plain English text like "Basic auth required" is left untouched.
- **Locale parity across all 12 locales.** Five chat and settings strings were English originals in all 11 non-English locale files. Translated in DE, ES, FR, HR, JA, KO, PT, RU, TR, VI, ZH. No English drift this release.
- **Linux install guidance updated.** Removed stale "(Beta)" label. Corrected artifact filename patterns. Clearer guidance for Ubuntu 24.04+, with the recommendation to use the `.deb` package on apt-based distros.
- **Friend Mode outreach reads context now.** Outreach used to pick a random keyword and write about it for days, in whatever language it felt like, with no record in your chats. It now reads from your persona, recent session, and memory before sending on Telegram, WhatsApp, or Email, and the message lands in your chat history tagged "via Friend Mode". Replying to the outreach lands in a session that already knows what was said. Language follows your preferences first, then mirrors what you wrote last.
- **MCP Add Server opens the form on arrival.** Previously the "Add Server" button on the MCP page dropped you in Settings with no tab and no scroll. It now opens Settings on the Integrations tab, scrolled to the MCP section, with the Add Server form open.
- **Importer fixes.** The migration picker labelled the GitHub Copilot Chat parser as "Microsoft Copilot", now corrected. Imports from Copilot and Gemini now upload reliably in the same path as ChatGPT and Claude.
- **File tools report where the file actually went.** In workspace-sandbox mode, write operations are redirected into a workspace folder. The result messages used to show the original path, which meant the model would try to read it back from the wrong place. They now show the actual resolved path, with "(redirected to workspace sandbox)" appended.
- **Empty provider responses retry instead of dying.** Providers occasionally return a 200 with no content. That used to surface as "No response received." Skales now retries empty responses the same way it retries network errors. Tool-only responses and deterministic safety refusals are deliberately NOT retried.
- **Provider health check no longer wastes quota.** When Skales fell back to a secondary provider it used to ping the primary every minute. That ate quota on free tiers and could invalidate prompt caches. Recovery now happens on the next real user request: the primary is tried first, and on success the fallback banner clears. The "Retry now" button still works.
- **Retry button on assistant error bubbles.** After all retries and the fallback chain are exhausted, you used to be left with no obvious next move other than retyping. A small Retry button next to the error message now re-submits your last user text. Attachments are not re-attached. Edit the message if you need to retry with a file.
- **Per-provider Timeout and Retries settings live under AI Providers now.** Both used to render inside the Memory tab, which is not where anyone looks for provider configuration. Moved to the bottom of Settings > AI Providers. Saved values are unchanged.
- **The "You can now customize your menu" upgrade notification is gone.** It was a v10.3.0 announcement that kept reappearing. Removed entirely. New users discover Add-Ons through normal navigation.

### Changed

- **Context-size badge loads in all builds.** A previous regression broke the badge in certain builds. Showing user-overridden limits in the badge is queued for v11.

### Migration sources

- ChatGPT, Claude, GitHub Copilot Chat, OpenClaw, and Cherry Studio all import end-to-end with both user messages and assistant responses.
- Gemini imports your prompts only. Google Takeout does not export model responses, so the assistant side of each conversation is empty by design.
- Hermes is supported as an import source. Most users will not need this. It is there for people migrating off Hermes specifically.

## v10.3.1

A maintenance release that cleans up rough edges from v10.3.0. Nothing new on the surface. Things that already worked now work the way the screens promised.

### Fixed

- **Custom Folders survive mode switches.** Saving settings while in Workspace Only or Unrestricted mode used to wipe the configured Custom Folders list. Switching to Custom mode would then show an empty list even though paths were configured. The list is now persisted in every mode.
- **OS notifications respect the Friend Mode OS-notify toggle.** Every incoming Telegram message used to fire a native desktop toast regardless of the Friend Mode setting. OS toasts now respect the setting.
- **Telegram replies are tagged "via Telegram" instead of "via Desktop Buddy".** Subsequent replies in a session used to be relabelled. The original source is now preserved on every turn.
- **MCP Start actually starts the server.** The Start button used to leave the status at "Stopped / 0 tools" even after clicking it. Start now spawns the process and lists tools immediately, the same way Test does. Stop kills the running process without touching the saved config.
- **MCP Edit opens a pre-filled form in edit mode.** Clicking Edit on an existing MCP server used to navigate to an empty Add form. The form is now pre-filled with the server's current settings; the button reads "Save changes" and there is a Cancel option to back out without persisting.
- **MCP template tiles pre-fill the Add form.** Clicking a template tile on the empty-state MCP page now fills in the form for that template instead of dropping you on a blank form.
- **MCP Logs button works.** The Logs drawer used to fail. It now opens and shows the captured logs for the selected server.
- **MCP configs with a combined command string work on all platforms.** A config like `"command": "npx -y obsidian-mcp-server@latest"` with empty args used to work only on Windows. macOS and Linux now handle it too.
- **Desktop Buddy drag stays smooth across the whole screen.** The mascot used to get stuck on Linux when dragged past a certain point. Drag now works the same way on macOS, Windows, and Linux.
- **Desktop Buddy snaps back to a visible display.** If the buddy ends up off-screen after a monitor change, sleep/wake, or rearrangement, it moves back to the primary display's bottom-right corner on the next show.
- **Desktop Buddy on Linux lets clicks through the transparent area.** The bottom-right rectangle around the mascot used to block clicks from reaching the desktop underneath. The buddy now passes clicks through wherever the mascot, the speech bubble, and the input pill are not.

## v10.3.0

A power-user release. The first genuinely native organisational surface (Project Tracker), a working RAG primer, a real command palette, Friend Mode that actually fires, a summarize flow that returns inline infographics, and a manual /cast page for DLNA. The minor-version bump is for the Project Tracker.

### Added

- **Project Tracker (`/projects`).** Linear-style local workflow inside Skales. Each project carries a title, description, status (idea, planning, in progress, paused, done), priority, tags, optional deadline with a colour-coded progress bar and "X days left" / "Overdue by X day(s)" caption, milestone list, notes, and attachments. From the detail view, "Discuss with AI" or "Start working" launches a chat session scoped to the project. From chat: `/projects` (list), `/projects new "Title" | desc` (create), `/projects status "Title" -> in_progress` (move), `/projects open "Title"` (jump in). Storage is local.
- **Friend Mode is alive again.** Proactive check-ins broke when Buddy Mode landed in v8/v9. They work now whenever Friend Mode is enabled, and toggling the master switch in Settings takes effect immediately.
- **WhatsApp and Email channels for Friend Mode.** WhatsApp sends to the first permitted contact. Email sends to your own configured address. The "Coming Soon" placeholder is replaced with a working toggle.
- **Cmd+K / Ctrl+K command palette.** Global fuzzy launcher across every visible nav item, every settings tab, and your 20 most recent chat sessions. Hidden in buddy, spotlight, and bootstrap windows.
- **/search command in chat.** Full-text scan over your saved sessions. Magnifier icon in the bottom-left composer toolbar prefills the composer. Results render inline as ranked snippets with one-click links back to the original chat.
- **/rag command and local Knowledge Base.** Paste documents on /memory (new Knowledge Base card). `/rag <query>` in chat returns the top matching chunks with source labels and scores. All local.
- **Summarize style topbar.** Click the summarize button to choose between text, markdown, HTML infographic, or extract-and-summarize. HTML mode renders inside a sandboxed iframe inline in chat with an editorial aesthetic. The composer stays clean while you type.
- **/cast page.** Manual surface for DLNA / UPnP discovery and control. Lists every device found, shows a discovery log, casts any HTTP media URL with play, pause, stop. Gated by the casting add-on, off by default.
- **13 new bundled templates** across chat, browser, studio, codework, code, planner, organization: Friday Weekly Review, Rubber-Duck Debugger, 2-Option Decision Matrix, Competitor Pricing Scan, Brand Palette Poster, README from Scratch, Deep Work Day Plan, Research+Write Pipeline, Launch Plan Starter, and more.
- **Knowledge Graph visualization.** The KG data store has existed since v9. `/memory` now renders a real graph view with a type-colour legend and hover-to-highlight when KG is enabled.
- **Brand Kit to Studio image bridge.** Brand colours, tone, and typographic direction are appended to every image-generation prompt now, matching what video generation already did.
- **DLNA discovery actually finds devices.** Discovery is more patient and runs in parallel with the fallback scan by default, so dual-band routers no longer under-report.
- Disabled add-ons disappear from all menus consistently.
- Add-Ons toggles take effect across every surface: sidebar, settings tabs, chat tools dropdown, chat quick actions.
- One-shot upgrade notification in the Notification Center explains that add-ons can now be toggled from the sidebar. Dismissed once, never reappears.
- Older sessions with legacy tool-call format play back without breaking chat replay.
- "Powered by GIPHY" and "Powered by Klipy" attribution in Settings, Chat, and Discover Feed.
- Add-Ons page reorganized into tabbed sections: Skales Tools, Communication, Integrations, Computer & Vision.
- Context-size badges next to model names in chat composer and settings, colour-coded by size.
- General settings group: default location and temperature unit. Powers the weather widget and the in-chat weather lookup.
- Multiple Google Calendar IDs supported. Plus-button in calendar config adds a row; reads cover all configured calendars.
- Jina Reader joins Tavily as a web-text extraction option, selectable in Settings > Providers.
- AppImage on Ubuntu 24.04+ now starts cleanly even on restricted kernels.
- Studio Gallery deletes now stick. Toast on failure.
- Reasoning blocks from local model runtimes render cleanly in chat.
- Consistent "Tool Result" label for tool result blocks.

### Changed

- Memory section moved out of the Security tab.
- Tavily and web extractor selector moved from Integrations to Providers.
- Studio and Integrations tabs consolidated.
- Settings search now matches correctly.
- Memory Consolidation (Dreaming) toggle saves on click. Most users finally see a nightly run.
- Sidebar order: Discover lifted above Notifications, Projects lifted above Agents.
- Toasts no longer surface in the buddy desktop pet, the mini-chat overlay, or the spotlight window. They appear only in the main app window.
- Notification action URLs that start with `/` now navigate in the same window instead of opening a new one.
- Set Up Vision Provider button deep-links into Settings instead of routing to Add-Ons.
- Skills renamed to Add-Ons across navigation, page headers, and subtitles.
- Beta indicators removed everywhere except Studio.
- Tools for unconfigured integrations no longer get sent to the model on every request. Big reduction in token usage on small-context local providers.
- ~250 untranslated placeholders cleaned up across 11 non-English locales.
- When you ask Skales "what can you do?", the answer now covers Projects, Knowledge Base (RAG), Chat History Search, Command Palette, Hugging Face Spaces, the Jina extractor option, multi-calendar fan-out, and the default-location weather behaviour.

### Fixed

- Friend Mode now sends proactive messages reliably.
- /search magnifier button is visible on desktop now (was hidden in the mobile-only icon row). Moved to the bottom-left composer toolbar.
- Summarize prefix "Summarize this (URL, Text..):" no longer leaks as visible text into the input bar.
- Mixed-language strings in Russian and Chinese gallery confirmation dialogs.

### Removed

- Deprecated legacy Custom OpenAI provider section.

## v10.2.12

### Added

- **Friend Mode is alive again.** Proactive check-ins broke when Buddy Mode landed in v8/v9. They work now whenever Friend Mode is enabled, and the master toggle in Settings takes effect immediately. No app restart needed.
- **WhatsApp and Email channels for Friend Mode.** WhatsApp sends to the first permitted contact. Email sends to your own configured address. The "Coming Soon" WhatsApp placeholder is replaced with a working toggle.
- **Cmd+K / Ctrl+K command palette.** Global fuzzy launcher across every visible nav item, every settings tab, and your 20 most recent chat sessions. Hidden in buddy, spotlight, and bootstrap windows.
- **/search command in chat.** Full-text scan over your saved sessions. Magnifier icon next to the slash button prefills the composer. Results render inline as ranked snippets with one-click links back to the original chat.
- **/rag command and local Knowledge Base.** Paste documents on /memory (new Knowledge Base card). /rag <query> in chat returns the top matching chunks with source labels and scores. All local.
- **Knowledge Graph visualization.** The KG data store has existed since v9. /memory now renders a real graph view with a type-colour legend and hover-to-highlight when KG is enabled.
- **Brand Kit to Studio image bridge.** Brand colours, tone, and typographic direction are appended to every image-generation prompt now, matching what video generation already did.
- **DLNA discovery actually finds devices.** Discovery is more patient and runs in parallel with the fallback scan by default, so dual-band routers no longer under-report.
- Disabled add-ons disappear from all menus consistently.
- Add-Ons toggles take effect across every surface: sidebar, settings tabs, chat tools dropdown, chat quick actions. Disabling Notion (for example) removes it everywhere until you turn it back on.
- One-shot upgrade notification in the Notification Center explains that add-ons can now be toggled from the sidebar. Dismissed once, never reappears.
- Older sessions with legacy tool-call format play back without breaking chat replay.
- "Powered by GIPHY" and "Powered by Klipy" attribution in Settings, Chat, and Discover Feed.
- Add-Ons page reorganized into tabbed sections: Skales Tools, Communication, Integrations, Computer & Vision.
- Context-size badges next to model names in chat composer and settings, colour-coded by size.
- General settings group: default location and temperature unit. Powers the weather widget and the in-chat weather lookup.
- Multiple Google Calendar IDs supported. Plus-button in calendar config adds a row; reads cover all configured calendars.
- Jina Reader joins Tavily as a web-text extraction option, selectable in Settings > Providers.
- AppImage on Ubuntu 24.04+ now starts cleanly even on restricted kernels.
- Studio Gallery deletes now stick. Toast on failure.
- Reasoning blocks from local model runtimes render cleanly in chat.
- Consistent "Tool Result" label for tool result blocks.

### Changed

- Memory section moved out of the Security tab.
- Tavily and web extractor selector moved from Integrations to Providers.
- Studio and Integrations tabs consolidated.
- Settings search now matches correctly.
- Toasts no longer surface in the buddy desktop pet, the mini-chat overlay, or the spotlight window. They appear only in the main app window.
- Notification action URLs that start with `/` now navigate in the same window instead of opening a new one.
- Set Up Vision Provider button deep-links into Settings instead of routing to Add-Ons.
- Skills renamed to Add-Ons across navigation, page headers, and subtitles.
- Beta indicators removed everywhere except Studio.
- Tools for unconfigured integrations no longer get sent to the model on every request. Big reduction in token usage on small-context local providers.
- ~250 untranslated placeholders cleaned up across 11 non-English locales.
- When you ask Skales "what can you do?", the answer now covers the v10.2.12 surfaces: Knowledge Base (RAG), Chat History Search (/search), Command Palette (Cmd+K), Hugging Face Spaces, Jina extractor option, multi-calendar fan-out, and the default-location weather behaviour.

### Fixed

- Friend Mode now sends proactive messages reliably.
- Mixed-language strings in Russian and Chinese gallery confirmation dialogs.

### Removed

- Deprecated legacy Custom OpenAI provider section.

## v10.2.9

Hotfix for Organization task lifecycle.

### Bug Fixes

- **Instant abort for Organization tasks.** The Abort button cancels in-flight calls within milliseconds now, instead of waiting for the current call to time out. No more wasted tokens on aborted runs.
- **Project deletion stops running tasks.** Deleting a project with an active task aborts the task first, so it stops cleanly.
- **Orphaned tasks cleaned up on startup.** Tasks left running after a crash or restart are marked aborted on next boot, so the UI does not try to resume dead tasks.

## v10.2.8

### Skales Mobile is Live on Android

Skales Mobile is now publicly available on the Google Play Store for Android phones and tablets. Connect to your Skales Desktop instance over the encrypted relay for full feature access, or run the standalone mode with 27 native mobile tools. Install from the Play Store at https://play.google.com/store/apps/details?id=app.skales.mobile.

iOS is in review with Apple. The Play Store launch is the public beachhead; iOS lands the moment review clears.

### New Features

- **Memory Mode.** The setting formerly known as Token Compressor is now called Memory Mode, with clearer mode names (Always Remember, Compact, Minimal) and a more intuitive UI. Minimal mode moved behind an Advanced disclosure to prevent accidental selection. When the active mode is non-default, a small amber Brain badge appears in the chat header to surface the state and provide a one-click jump back to settings. Hidden in Mini Mode, matching the established pattern for Voice, Call, Share, and Incognito.
- **Codework resume banner.** When the most-recent Codework session was paused or interrupted, a dismissible resume banner appears at the top of the welcome view with Resume and Dismiss buttons. The banner replaces the previous Continue button as the canonical resume affordance. Dismiss transitions an active session to a stopped state and persists so the banner does not reappear.
- **Codework file-tree toggle.** New header button toggles the file tree pane visibility, with state persisted across restarts.
- **Codework recent sessions sorted by activity.** Recent sessions now sort by last-touched time instead of filename. Status badges expanded to five states: IN PROGRESS, DONE, ERR, STOPPED, or none for unknown.
- **Sidebar agent filter.** The sidebar now filters sessions by the currently selected agent, so clicking a session no longer reroutes to a different agent's context.
- **Open Folder button.** Codework projects now have an Open in Finder / Open in Explorer button in the project header.
- **OpenRouter as default provider.** New installs and first-time setups now default to OpenRouter as the primary provider for faster onboarding.
- **Tasks expand modal.** Long task results no longer truncate silently. Click any task to open a full-text modal.
- **Persona persistence.** Selected persona now persists across conversation restarts.

### Bug Fixes

- **Session writes no longer clobber each other.** Concurrent writes from chat, buddy, mobile bridge, Telegram, and Spotlight could previously overwrite each other. They now serialize correctly.
- **Tool-only assistant turns no longer vanish.** Interrupted tool calls used to be silently dropped from the chat. They now render as an italic indicator showing the attempted tool names, preserving the conversation timeline.
- **Skill AI and GPT-5.x consolidation.** Edge cases in the GPT-5.x reasoning detection added in v10.2.7 are consolidated and covered.
- **Tool pruning logic.** A regression in tool-pruning for large conversations is corrected.

## v10.2.7

Hotfix for three user-reported regressions surfaced after v10.2.6. No new product surfaces. Auto-updater pipeline unchanged.   Locale parity preserved.

### Bug Fixes

- **OpenAI GPT-5.x models failing with 400 errors.** v10.2.6 detected o1, o3, o4, and bare gpt-5 but missed every GPT-5.x dot-version. Detection now covers the full lineup (gpt-5.1 through gpt-5.5) including every documented suffix and dated snapshot. Works regardless of routing path (direct, OpenRouter, Custom Provider, or any OpenAI-compatible relay).
- **Custom Provider 404 errors.** Base URLs that already include a version segment or full path are detected and not duplicated. Z.ai, Groq, and other providers with non-default URL structures work out of the box. Same detection applies to model-list discovery.
- **Telegram proactive messages returning provider errors.** Friend Mode, Identity Maintenance, daily standup, and cron task completion notifications now go through the same path as in-app chat, so reasoning-model handling stays consistent across every send site.

## v10.2.6

Mini-release focused on critical user-reported bugs across the OpenAI and Gemini providers, sleep/wake recovery, capabilities awareness, and the Planner. No new product surfaces. Auto-updater pipeline unchanged.  

### Bug Fixes (Critical, User-Reported)

- **OpenAI provider 400 across all models.** OpenAI's chat completions API changed conventions in late 2025. Skales now uses the current field names and reasoning-model conventions per model class.
- **Gemini tool calling broken (thought signature missing).** Multi-turn tool conversations with reasoning-enabled Gemini 2.5 Pro and Flash now work.
- **Sleep / wake white screen.** Returning from system suspend or unlocking the screen no longer leaves Skales stuck on a blank page.
- **Capabilities awareness.** Skales knows about its own UI features when asked: math rendering, HTML preview, code-block actions, edit / branch / delete on user messages, manual compact, token-split tooltip, MCP servers, and the live skills system. Asking "can you do math?" or "can you render HTML?" returns "yes" with the right context.
- **Planner: weekly schedules fired daily.** The day-of-week selection used to default to Mon-Fri, which made any "weekly" schedule fire every weekday. Defaults now reset to a single day when you switch to weekly.
- **Planner: day-of-week selections respected.** Picking "Monday + Wednesday" now fires only on Monday and Wednesday.
- **Planner: tasks not visible in Tasks list.** Recurring tasks created in the Planner now appear in the global Tasks page alongside one-off tasks. Each Planner run still produces its own execution entry with full logs.
- **Telegram proactive Friend Mode messages not firing since the Desktop Buddy proactive feature was added.** Friend Mode check-ins, autopilot approval notices, daily standup delivery, and cron-task completion notifications all fire correctly on Telegram again, surviving bot restarts.
- **MCP Servers tab failing to load with 401 error.** Now loads correctly.

## v10.2.5

### Bug Fixes (Critical)

- **Skills system.** Skill toggles were silently ignored since the feature shipped. Computer Use Tools, Calendar Reminders, and other skills now actually function when toggled.
- **UI Skill Toggles.** Toggles persist across restart. Previously, toggles appeared to switch on but reverted after reload. Silent backend errors now trigger UI rollback.
- **Playground Override Persistence.** "Use active model" for per-mode overrides no longer springs back to the previous selection after Save.
- **MCP State Reporting.** The model reports MCP server status correctly instead of always claiming "off".
- **MCP backend status.** Returns real per-server status (disabled, connected, stopped, error) instead of hardcoded "connected" with 0 tools.
- **Calendar Reminders Endpoint.** Was permanently skipped due to a broken setting read. Now wires up correctly.
- **Proxy support.** All provider sites now route through proxy-aware HTTP. Runtime ECONNREFUSED errors with proxy enabled are gone.
- **Token Cap Cloudflare and NVIDIA.** Bumped from a conservative floor up to the real provider ceiling.

### Features

- **KaTeX Math Rendering.** Inline `$E=mc^2$` and block `$$...$$` render as actual mathematics.
- **Edit, Branch, Delete on User Messages.** Hover actions on user bubbles. Edit triggers truncate-and-resend. Branch creates a new chat from that point. Delete removes the message and all subsequent responses.
- **Manual Compact Button.** Compress chat history on demand instead of waiting for the auto-threshold.
- **Token Split Tooltip.** Hover the token badge to see input and output split. Default display unchanged.
- **Code Block Copy.** Hover any code block to reveal a Copy button. 2-second "Copied!" feedback.
- **Code Block Ctrl+A Scoping.** Ctrl/Cmd+A inside a code block selects only that block, not the whole window. Works for Markdown code blocks AND HTML preview blocks.
- **HTML Preview Copy.** Copy button added to the HTML preview header alongside Download HTML.
- **Identity Maintenance Toggle.** Moved to its dedicated section in Settings (was buried under Agent & Tasks).

### Stability / Polish

- **Header Responsive Layout.** Buttons collapse to icon-only on narrow windows with native tooltips on hover. Header no longer breaks on tablet portrait or narrow desktop.
- **Provider Switcher Dropdown.** Anchors correctly in chat and playground. No more left-side cutoff.
- **Compact Button hidden in Mini Mode.** Mini Mode stays minimal.
- **Compaction context retention.** Recent tool calls are preserved as examples after auto-compact.
- **MCP servers no longer crash the app.** Unhandled errors from misbehaving MCP servers are caught.
- **Token Display Tooltip.** Power users see input and output split, casual users see unchanged total.

## v10.2.2

### Provider layer

- **Live model fetch for cloud providers.** Each provider card in Settings > AI Providers now has a Refresh button. Anthropic, OpenAI, Google Gemini, Groq, DeepSeek, Mistral, xAI, Together, MiniMax, Cloudflare, NVIDIA, SambaNova, and Cerebras all support live model listing. Clicking refresh updates the model picker. Model dropdowns prefer the live list when present and fall back to the built-in baseline. New models become usable without a Skales release.
- **User-configurable model limits.** New collapsible "Override Model Limits" section under AI Providers. Add per-provider, per-model override rows for context and output token caps. Use `*` as the model name to apply the limit to all models of that provider that don't have an explicit override. Useful for newly released models whose limits differ from the built-in registry.
- **Per-provider proxy now actually routes.** v10.2.0 declared the feature but it was not wired through correctly. Fixed.

### Chat

- **Manual message delete persists across reload and restart.** v10.2.0 trimmed the in-memory message list but did not save the deletion. Now saved.
- **Branch action creates the correct slice.** v10.2.0 surfaced the Branch button but the slice index was wrong. Clicking Branch on the Nth message now creates a new session with messages 1 through N inclusive.
- **Bubble action labels and toasts are translated.** A handful of locale keys shipped without translations in v10.2.0. Real translations added across all 12 locales.

### Settings

- **Per-Mode Model Override UI uses real provider data.** The dropdowns previously showed a hardcoded curated list regardless of what you had configured. Now mirrors the chat header picker: provider list shows only enabled providers with API keys, model list reflects your configured plus live-fetched models for the chosen provider.
- **Playground on-page picker and Settings Per-Mode Override stay in sync.** Both surfaces persist to the same place and rehydrate from disk on every change.
- **The "?" agents-info trigger no longer appears on the Settings page.** It belongs on the Agents page only, which is unchanged.

### UI

- **Chat and Playground header model pickers hide on small viewports.** On narrow widths the picker is hidden entirely, matching the existing Mini Mode behavior.

### Notes

- Settings schema additions are optional with safe defaults. Existing settings load unchanged.
- 12-locale parity preserved.

## v10.2.0

Iterative quality release across providers, modes, error UX, and chat history. No new product surfaces. Existing surfaces become more resilient and configurable.

### Provider layer

- **Per-provider, per-model limits.** Context window and max output tokens are now looked up per provider and per model. Replaces the previous hardcoded fallback ceilings used across all modes. Unknown models fall through to per-provider defaults, then to a conservative absolute fallback. Live values from HuggingFace Router can override the static entry at call time.
- **Smart context compaction with LLM summary.** When effective context exceeds the budget threshold, older turns are summarized in a single low-cost call instead of being truncated. Falls back to truncation if the summary call fails.
- **Provider error translation.** Raw provider error bodies are translated into actionable user messages with the right next action. Covers Ollama crashes and missing models, OpenRouter rate limits and missing credit, Anthropic and OpenAI context-exceeded and auth issues. Generic fallback adds provider and status for debugging.
- **Per-provider proxy support.** Provider configs accept an optional proxy setting. When set, requests to that provider route through the proxy. Existing settings load unchanged.
- **Multiple custom OpenAI-compatible endpoints.** Settings now supports more than one custom endpoint at a time. The legacy single Custom Provider continues to work and is auto-migrated. Per-entry: label, base URL, API key, model, enabled, tool-calling, and vision toggles.

### Modes

- **Per-mode model resolution.** Each Skales mode resolves provider and model from: explicit caller override, then per-mode override in Settings, then the global active provider and model. Exposed in Settings as a "Per-Mode Model Overrides" panel covering Chat, Codework, Organization, Studio, Playground, Buddy, Spotlight.
- **Playground respects active model.** The previous silent override to Anthropic Sonnet 4.5 is now an opt-in setting (default OFF). When OFF, Playground uses the active provider and model or the per-mode override. Toggle and per-conversation provider and model picker now live on the Playground page header.
- **Inline model picker in Chat.** Compact icon-only button in the chat header opens a popup listing installed providers and curated models. Layout never shifts. Background respects the active light/dark theme. Custom agent provider and model wins over the global default. Picker is hidden in Mini Mode.
- **Chat command `/model <id>` persists.** The slash command writes the new model to your settings, refreshes the picker, and surfaces a clear error bubble on save failure.

### Tools and routing

- **Calendar routing disambiguation.** Natural-language calendar phrasing ("in Google Cal", "in den Kalender", "gcal", "schedule a meeting at TIME") routes to the calendar tool reliably. Planner is now clearly for autonomous Skales prompts, not human appointments.
- **Tavily gate respects user toggle.** When the Tavily skill is disabled or no Tavily API key is configured, web search is filtered out of the tools sent to the model. Other web tools remain enabled.
- **Tool prune for small-context providers.** Providers with small token-per-minute ceilings (such as Groq) automatically receive a pruned tool list, preventing "request too large" errors on free tiers.

### Autopilot

- **Master Switch persists from the Autopilot page.** Toggling the master switch from the Autopilot page (not just Settings) now persists alongside heartbeat start/stop. Activate-on-Start works from a clean restart regardless of which UI surface the toggle came from.
- **Friend Mode observability and persistent cooldown.** Skip reasons are logged. Cooldown survives app restart so it no longer resets to zero on every launch.
- **Identity maintenance auto-approve.** Optional Settings toggle. When enabled, the daily identity maintenance job bypasses the safe-mode and critical-action approval gates. Logged to the audit trail. Default OFF.

### Chat UX

- **Manual delete and branch from any message.** Hover actions on user and assistant message bubbles. Delete trims trailing tool messages so no orphan tool results remain. Branch creates a new session populated with messages up to the chosen point.
- **Resume action on error toasts.** Provider error toasts now carry an action button keyed by the error type: continue or retry on local-model crashes and timeouts, compact on context-exceeded errors, switch-fallback on rate limits and quota errors, open-settings on auth failures.
- **Continuation on output truncation.** When a model stops because it ran out of output room, Skales now asks it to continue instead of dropping the partial answer.

### Settings UX

- **Three-fallback UI cap removed.** The Fallback Chain section accepts as many entries as you want to add.
- **Per-Mode Model Overrides panel.** New section under AI Providers. Per mode: "Use active model" toggle plus provider and model dropdowns when the override is on.
- **Additional Custom Providers panel.** New section under Advanced. Each entry is a card with label, base URL, API key, model, enabled, tool-calling, and vision toggles. Add and remove buttons. Legacy single-slot Custom Provider UI continues to work.
- **Identity Maintenance Auto-approve toggle.** Lives under Autopilot in v10.2.0. Will move under Settings > Memory near the existing identity maintenance controls in a follow-up.

### Discover

- **Layout fixes for long-form posts.** Post text wraps on long URLs. Category badges truncate when very long. Header row wraps when content is wide. Skales Insider posts no longer break the feed layout.

### Telegram

- **Approval flow shows continuation hint.** After a Telegram-approved tool runs, the result message ends with "Tap or send Continue to resume." so you know the agent flow is paused, not finished. Optional auto-resume setting (default OFF) lets the agent continue automatically.

### Updater notifications

- **Changelog field carries through the update notification.** The auto-update notification now includes the full changelog whether the trigger came from a server check or an automatic detection.

### Localization

- 11 new error-translator keys plus 3 new model-picker keys, translated across all 12 locales (de, en, es, fr, hr, ja, ko, pt, ru, tr, vi, zh).

### Notes

- Settings schema additions are optional with safe defaults. Existing settings load unchanged.

---

## v10.1.1 - Hotfix

Five hotfix items rolled up on top of v10.1.0 Design. No new features.

### Vision routing

- **Vision-capable model detection extended.** Gemma 3, Gemma 4, LLaVA, Pixtral, Qwen-VL, Qwen2-VL, Qwen2.5-VL, and MiniCPM-V variants are now recognised. Image inputs route to the configured vision model correctly.
- **Explicit user override is respected.** If you have set a Vision Model in Settings, it is used regardless of whether auto-detection knows about it. Auto-detection becomes a fallback, not a gate.
- **Tool-call hardening.** Malformed tool-call JSON is parsed forgivingly. On total failure, one explicit retry asks the model to return valid format. Tool execution errors are surfaced into the next turn so the model cannot silently report success on a failed write or denied permission.

### Chat history

- **Mobile-origin badge.** Messages synced from Skales Mobile now show a small phone icon next to the timestamp. Tooltip reads "Sent from Skales Mobile". Read-only visual cue, no behavioural change.

### Autopilot

- **Activation modes.** Settings on the Autopilot page now include an Activation Mode picker with three options: Manual (legacy default, last toggle persists), On Startup (heartbeat starts automatically when Skales launches if the master switch is on), and Time Window (heartbeat is active during a user-defined daily 24-hour window such as 09:00 - 17:00). Time windows that cross midnight are supported (e.g. 22:00 - 06:00). Manual toggles inside an active window are respected for the rest of that window so you can pause without fighting the auto-activation. Existing v10.1.0 users default to Manual and see no behaviour change unless they explicitly switch modes.

---

## v10.1.0 "Design"

The biggest creative update yet. Skales Studio gets a Design Tab that turns prompts into real HTML and CSS designs. Codework matures into a full autonomous coding agent. HF Spaces and MCP servers now work everywhere. Smoother animations across the app.

### Studio

- **Studio Design Tab** is the new first tab in Studio. Type a prompt, pick a template (Landing Page, Dashboard, Mobile Screen, Pricing, Hero, Login, Settings), get usable HTML and CSS back. Live preview, palette extraction, font extraction, fullscreen preview (Escape to exit), inline refine drawer, recent designs dropdown. Designs persist between sessions.
- **Studio Image Generation revival.** HuggingFace image provider now works for SDXL, FLUX, and other models. Clear error messages on failure with provider-fallback suggestions.
- **HTML extraction is more forgiving** of common LLM output variants (fenced, unfenced, with or without DOCTYPE, truncated). Smaller models that don't follow exact output format still produce usable designs.
- **Smooth tab crossfades in Studio** on supporting browsers. Graceful fallback on Firefox.

### Codework

Codework matured significantly across the v10.0.4 to v10.1.0 cycle. It is now a full autonomous coding agent, not just a chat with file tools.

- **Approval Gates.** Three new toggles: auto-approve writes, auto-approve exec, auto-approve all. Conservative defaults (Review mode) for first-time users.
- **Forbidden command denylist** expanded. Common destructive operations (`rm -rf $HOME`, fork bombs, dd to disk, etc.) are blocked even with auto-approve enabled.
- **Test loop with progress guardrail.** Codework can run a test command after each code change. After repeated test failures with no progress, the loop aborts and reports back instead of grinding tokens.
- **Preview Mode** for write operations. Write tools generate diffs for you to accept or reject before applying.
- **MCP tool consumption.** Connected MCP servers are now exposed as tools to Codework. Conservative auto-approval gating.
- **Repository-map indexing.** Codework builds a project-wide map of functions, classes, and exports per file. Scales to 500+ files.
- **Long-context tiers.** For huge projects, Codework adapts what it includes in context based on project size.
- **Token usage tracking.** Live token counter shown in Codework, updating per call.
- **Commit-message generator.** New helper drafts commit messages from staged changes following Conventional Commits style.

### Cross-tool integration

- **HF Spaces and MCP everywhere.** Activated HF Spaces and connected MCP servers are now usable from Chat, Codework, AND Studio. Add a Space once, use it anywhere.
- **Active Tools Across Skales** panel in Settings shows which tools are available in which surfaces (Chat, Codework, Studio).
- **Studio can invoke any active HF Space directly.**

### Lio AI

- **Recursive project snapshot.** Lio AI builds a complete file map of your project on each plan and build cycle. Better context awareness for multi-file changes.
- **Plan context** assembled from project structure and chat history for higher-quality plans.

### Chat

- **Short queries keep their tools.** Queries like "Explain X with examples" no longer get stripped to plain chat by the auto-prune heuristic.
- **Exact token counting for context-budget calculations.** Tools are pruned only when they actually exceed provider rate ceilings.
- **Smoother streaming token rendering.**
- **Update toast localized** in all 12 locales with version interpolation. Update detail page renders the actual changelog.

### Stability

- **File reading and writing hardened.** Malformed JSON, missing files, BOM characters, and partial writes no longer crash the app.
- **Hierarchy clarity in Organizations.** Person, Agents, and Organization no longer get confused in multi-agent setups.
- **Namesake trope removed** from all 12 locales (was a phrase pattern that drifted across localizations).
- **Codework session sidebar** active-state correctly handles trailing slashes (no more two-row highlights).
- **Delete session prompt** now properly localized in all 12 languages.

### Mobile

- **Outbox foreground sync.** Pending messages flip to "failed" immediately when the app resumes.
- **Periodic outbox sweep** keeps state fresh during active chat use.

### Landing

- **Migration sources.** Landing page and Migration Importer now list OpenClaw, Hermes, and Cherry Studio alongside ChatGPT, Claude, Copilot, Gemini.
- **Three new feature blocks**: ComfyUI (local image generation), HF Inference Providers (200+ models), DeepSeek V4 (1M context, agent-tuned).

### Note on Lio AI export from Studio

The "Open in Lio AI" export from the Studio Design Tab was removed. Lio AI and Studio Design are different workflows and the link did not work. Use the Download HTML button instead.

## v10.0.4 - April 20, 2026

### Telegram Integration
- **Fixed**: Safe Mode approval flow broken since v9.x. Tool approvals from Telegram now correctly trigger the approval prompt and execute on your "yes" response
- **Fixed**: Telegram bot no longer requires opening the chat page after app launch to come online. The bot starts automatically once the app is ready

### Provider Presets
- **Added**: Minimax, Cloudflare Workers AI, and Nvidia NIM as first-class provider presets with pre-filled endpoints. No more manual Custom OpenAI-Compatible configuration needed for these
- **Added**: "Show only active" toggle in the Providers list to hide unused providers

### Chat & UX
- **Added**: Response time display on assistant messages, see how long each response took
- **Added**: Global hotkey `Cmd+Shift+H` (macOS) / `Ctrl+Shift+H` (Windows/Linux) to toggle Desktop Buddy visibility. Handy for fullscreen video
- **Improved**: Settings search now covers more sections, handles accents (é matches e, ä matches a), and has better keyword coverage in German, Spanish, French, Russian
- **Improved**: Fallback provider banner reworded for clarity with a details modal explaining why the fallback activated and how to fix the primary

### Export & Remote Access
- **Fixed**: Export via Tailscale or remote browser access no longer returns a corrupted HTML file instead of a ZIP.

### Email
- **Improved**: Outlook, Gmail, and Yahoo IMAP authentication errors now explain the App-Specific Password / OAuth2 requirement (Microsoft disabled Basic Auth in 2022) instead of showing a generic "auth failed" message

### Build & Infrastructure
- **Fixed**: Boot log now reports the correct version on every release

### Locales
- All 12 locales (en, de, es, fr, hr, ja, ko, pt, ru, tr, vi, zh) updated with v10.0.4 strings. Informal register maintained.

---

## v10.0.3 - Stability (April 18, 2026)

### Bug Fixes
- **Bonjour/mDNS Collision.** Multiple Skales instances on the same machine no longer shadow each other in swarm discovery
- **Multi-Agent Dispatch Toast.** Completion notification was silently dropped after all subtasks finished. Now fires with job title and subtask count.
- **Update Page i18n.** "Later" button showed a raw key instead of translated text. Translation added to all 12 locale files.
- **Ollama Small-Model Warning.** Settings panel shows an orange warning when a known small model (3B params or fewer) is selected with Max tools above 0, advising you to reduce tools or switch to a larger model.
- **fal.ai Studio Hang.** Video generation polled a stale URL after fal.ai changed their queue structure. Fixed.
- **Codework UI Lag.** Blank activity panel during the first second of a session replaced with a "Starting session..." indicator so the UI never appears frozen.

---

## v10.0.2 - 2026-04-18

### Fixed

- **Tool Filter Regression (critical).** Disabled the context-aware tool filter introduced in v9.2.1 which was silently dropping core tools. This was the root cause of the widespread "Unknown tool" errors after v10.0.0. All tools are now available in every conversation.
- **Export Dialog "Source path not allowed".** Exports failed silently in v10.0.1 because the OS temp directory was not on the allowed source-path list. Fixed.
- **Multi-Step Exit Guard.** Response text emitted alongside tool calls used to be misread as an exit signal, breaking multi-step tasks. Fixed.

### Improved

- **Per-turn cap on identical tool calls raised.** The previous cap was too low to support normal bulk operations like creating multiple folders or writing many files. Infinite loops with identical arguments are still caught by stall detection.
- **Progress-Speak Auto-Continue.** When a model makes tool calls but then stops with short progress-style text ("let me continue", "remaining", etc.), Skales automatically re-prompts it to finish instead of exiting. Mitigates Sonnet 3.7's mid-task pause behavior on bulk operations.

### Known Issues

- Sonnet 3.7 may still pause on large bulk tasks despite auto-continue. Use Claude Opus 4, Sonnet 4, or Minimax for guaranteed single-shot bulk execution.
- Minimax models may emit tool calls as JSON text in chat instead of real function calls for some prompts.
- Codework UI may appear briefly frozen before streaming catches up. The work is happening.
- Multi-Agent Dispatch toast notification is not currently firing. Check the Tasks tab for progress.

---

## v10.0.1 - Hotfix (April 17, 2026)

### Critical Fixes
- **Auto-Updater Schema Mismatch** resolved. Zero successful auto-updates from v10.0.0 was caused by this. Users on v9.x must install v10.0.1 manually once.
- **Export/Import.** Valid zip archives with credential redaction. Accepts legacy formats for backward compat.
- **Multi-Step Tool Chains** no longer exit prematurely when the model returns empty text between tool calls.
- **Advisor Strategy.** Simple chat no longer routed through the expensive Planner.
- **Gemini Tool Schema.** Stripped vendor-specific fields that Gemini silently rejected. Tool calls restored for Gemini models.
- **Calendar reminders and planner tasks no longer get confused.**

### Integrations
- Telegram outbound Chat-ID persists across bot restarts
- SMTP Test race condition eliminated
- Lio AI provider dropdown labels no longer show raw translation key paths
- Custom endpoint URLs (LM Studio, Ollama) accept any capitalisation

### Agent Behavior
- File access works in the configured language without hallucinated "system restriction" excuses
- Folder structure creation supports recursive paths in a single tool call
- Agent checks local state before suggesting support

### Configuration
- New Setting: Request timeout slider for long agent tasks
- Emoji loader stops spamming the console for missing animations

### Discover Feed
- 29 v10 event templates now render custom text

### Security (PHP + WordPress)
- Feed backend: IP hashing aligned across all endpoints
- WordPress Connector 1.2.1: improved token comparison and file upload safety

### Localization
- 132 new settings provider strings across 12 locales
- 4 chat state messages moved from hardcoded English to translation
- Request timeout setting fully translated

### Known Issues
- Reasoning display still abbreviated (deferred to v10.1)
- macOS notarization not yet implemented. Right-click then Open is required on first launch.
- SSH key authentication in SSH tool deferred to v10.1

### Upgrade Path
Auto-update works from v10.0.1 onwards. Users on v9.x: download manually from skales.app once.

---

## v10.0.0 - "Closing the Gap" (April 16, 2026)

The biggest Skales release ever. Desktop, Mobile, and Relay now form one ecosystem: every message you send from your phone routes through Desktop's full tool set, every capability you build on Desktop is reachable from the Mobile companion. Chat feels smoother. Studio speaks video. Settings speaks voice.

### Skales Mobile (NEW)
- Official Skales Mobile app for Android (iOS coming), submitted to Play Store (beta, closed testing)
- Full standalone AI agent in your pocket. 27 mobile tools, works with or without the desktop running.
- Remote Mode: pair via QR over the end-to-end encrypted relay. Keys never leave the devices.
- Paired phones get full access to your desktop's full tool set (shell, files, browser control, email, calendar, Studio, etc.)
- Image upload from mobile forwards to the desktop and gets analyzed like a local upload
- Shared ecosystem: same Discover Feed, same Custom Agents, same Skills

### Studio - LTX-2.3 Video Generation (NEW provider)
- fal.ai LTX-2.3 integration (text-to-video and image-to-video, standard and fast variants)
- $0.06/sec at 1080p, native 9:16 portrait support, 5s and 10s durations
- Added alongside existing Veo, Kling, Runway, Replicate providers
- Live "Connected" badge in Studio when fal API key is configured in Settings
- 4 new localized model labels across all 12 languages

### Animated Emoji System (NEW)
- Noto Color Emoji font bundled. All Unicode emojis render identically on Windows, macOS, and Linux.
- 16 brand and expressive emojis with smooth animations
- Animated splash screen. Gecko mascot animates during app startup.
- Dashboard wave. Hover over the greeting hand for a welcome animation.
- Discover Feed spark picker. Emoji reactions animate on hover, play once in the sent confirmation.
- Chat expressiveness. AI messages with creative, memory, video, or web context emojis animate on arrival.
- Big emoji messages. Send 1-3 emojis alone and they render larger with animation (iOS/Telegram style).
- Easter egg shortcuts in chat: `:gecko:`, `:bubbles:`, `:paw:`, `/highfive`, `/bow`
- Emoji privacy controls. Optional Google CDN fallback in Settings > Privacy (off by default, GDPR compliant).
- Brand emojis preload at startup for instant rendering

### Voice - TTS and STT
- OpenAI TTS provider added (voices: alloy, echo, fable, onyx, nova, shimmer). Reuses the existing OpenAI provider key, no extra setup.
- New "Read responses aloud" toggle in Settings > TTS. When enabled, every assistant reply is spoken via the configured provider once streaming completes.
- Smart markdown stripping before TTS so the voice doesn't read ``` or # out loud
- Per-message speaker button on every assistant bubble. Click to listen, click again to stop.
- Groq-key hint in the STT section (free Whisper access) with one-click jump to AI Providers tab
- All voice UI fully localized across 12 languages

### Chat - Smoothness and Inline Preview
- Smooth spring entrance for new messages
- Session restores stay instant. Only new messages fade in.
- Typing indicator rewritten as a smooth wave
- Typing bubble fades in instead of popping. The indicator never jumps when transitioning to tool-status.
- Scroll-to-bottom button appears bottom-right when you scroll up. Click to return to live view and re-enable auto-follow.
- Inline HTML Preview. HTML code blocks render a live preview with Show Code, Download HTML, Save as Image, Mute, Hide toggles.
- Global mute and hide persist across all chats and sessions
- Save as Image. Pixel-exact capture.
- OS reduced-motion setting disables all animations across the app

### Capabilities & System Prompt
- Version 10.0.0 named "Closing the Gap"
- Skales now knows about Mobile pairing, Inline HTML Preview, the fal.ai video path, Remote API, and the 12 supported languages when asked
- Agent is proactive: when you describe something visual (chart, card, map, SVG, mini-app), it offers inline preview in your language before producing it
- Mobile pairing info is now exposed to the agent
- 6-theme awareness (Dark, Light, Midnight, Forest, Amber, Glass)

### Bug Fixes
- Buddy window draggable on Windows. Cmd/Ctrl+Shift+B global reset shortcut.
- Agent delete button fixed. Replaced with a two-click confirmation directly in the button.
- Playwright chromium detection always picks the newest version installed
- Telegram bot auto-restart watchdog with rate-limit (max 3 respawns per rolling hour). Respects current config.
- Renaming your gamertag updates the existing record in place instead of creating a duplicate
- STT help text shows the translation instead of a raw key
- Studio video-provider "(API key required)" badge now translated across 12 languages
- Boot log now matches the current version (was reporting 9.3.0)
- External links in browser view now open in the default OS browser
- Share window overlay responds to Escape key across chat, spotlight, and buddy
- Browser scroll-to-bottom works reliably on lazy-loading and single-page applications
- Playbook steps now wait for actual page load before proceeding
- macOS screen recording permission detected with user-facing guidance in share window

### Localization
- ~60 new strings added across 12 languages for fal.ai models, HTML preview, voice, Mobile, animated emojis, mute/unmute, scroll-to-latest
- All new German translations are Du/Sie-neutral ("Vorlesen", "Stumm", "Als Bild speichern")

## v9.3.0 - Stability Release (April 13, 2026)

### Stability
- File reading hardened against malformed JSON across the app. Fixes a Windows crash.
- Playground Bridge rewrite. Duplicate injection removed, no more dead buttons.
- Auto-updater fix. Download button now appears correctly, race condition resolved.
- Advisor model validation auto-corrects model IDs for OpenRouter, falls back to primary model on 400/404.
- Playground output cap raised (chat unchanged).
- Skill loading errors consolidated to a single summary line.
- Integrity check downgraded from a warning to an info log.
- Discover Feed admin posting no longer crashes.

### Memory & Intelligence
- Identity Maintenance untouched (runs at 3:00 AM as always)
- Memory Consolidation staggered to 3:30 AM. No overlap with Identity Maintenance.
- Knowledge Graph shows helpful hint when empty instead of "0 entities"
- Agent capabilities awareness updated to v9.3.0 with all current features

### Browser & Automation
- Persistent browser sessions. Logins survive restart.
- Playwright install resolves the binary reliably without depending on the system path.

### Localization
- Removed all "Coming in v9.2.1" text from 12 locale files
- Version strings corrected to 9.3.0 everywhere

## v9.2.5 - "WordPress 2.0" + Playground (April 13, 2026)

### WordPress 2.0
- WordPress Design Skill bundled with 15 Elementor and 10 Gutenberg templates
- Elementor Flexbox Container format (fixes blank pages on modern Elementor)
- Canvas page template auto-set
- Web search available in the WordPress agent
- Selective skill loading keeps the prompt lean
- WordPress Connector Plugin v1.2.0 with collision detection

### Playground (Beta)
- Deep onboarding interview: 15 questions, 4 phases
- AI-powered personalized Space suggestions
- Glassmorphism UI with animated mesh background
- AI-generated interactive Spaces that persist
- AI features wired into the Playground page directly
- Share to Discover Feed with data sanitization
- Milestone system

### New Tool
- **File download tool.** Auto-filename, redirects, VirusTotal scan.

### Fixes
- Duplicate file tools unified
- Slash commands: typed and clicked now identical (all 24 commands)
- /theme toggle fixed
- Memory Consolidation catch-up scheduler
- Playwright install on macOS more reliable
- Discover Feed admin delete and admin free posting
- Studio: Veo 3.0, Imagen 4, GPT Image 1, Kling v2, Runway Gen4 Turbo
- Obsidian theme header navigation updated
- Tool deduplication prevents duplicate function errors
- Tool-awareness warning for local models with tools disabled

## v9.2.3 - File Operations & Stability (April 2026)

### Critical Fixes
- **File tool routing.** Folder and directory tools unified. Tilde expansion now works across all file tools.
- **Model routing.** Skales is now explicit with the model about which tool to use for each file operation.
- **Multi-step tasks.** Agent no longer stops after the first tool call on incomplete directory and file creation.
- **Sidebar version.** Now shows the correct version number (was stuck on 9.2.1).
- **Slash commands.** All 24 commands audited; /tools list updated.

### Improvements
- Duplicate file tools merged. Old names kept as aliases for backward compatibility.
- Duplicate tool definitions removed. Models see one tool per operation.
- Document creation expands tilde paths.
- /tools slash command lists the correct tools.

### Verified (no changes needed)
- Auto-updater: full check, download, verify, install flow confirmed working
- Playwright install: path resolution and error handling all solid

## v9.2.2 - Hotfix (April 2026)

### Critical Fixes
- **Network access restored.** Tailscale, LAN, and remote access work again.
- **Auto-updater.** Full download with progress bar, integrity verification, install and restart UI. No more "Download at skales.app" link.
- **Playwright install.** Install buttons work on systems where brew or npx are not on the default path.
- **Custom Endpoint status.** "Not connected" no longer shown for working local endpoints (LM Studio, KoboldCpp).
- **Custom Endpoint tool slider.** Description updated to mention LM Studio and KoboldCpp.

### Improvements
- Discover Feed admin delete button (for moderators)
- Playwright detection covers more install locations

---

## v9.2.1 - Stability & Completeness (April 2026)

### Ollama / Local Models
- **Tools disabled by default for local models.** Ollama, LM Studio, KoboldCpp, vLLM no longer receive tool definitions. Eliminates timeouts on consumer hardware.
- **Tool slider.** New "Max tools for local models" slider. Shared across all local providers.
- **Fast-fail retry.** If a local model doesn't respond promptly with tools, Skales retries without tools automatically.
- **Timeout leak fix.** Custom endpoint timeout used to override Ollama's longer timeout for all providers. Now scoped to custom endpoints only.
- **Chat hard-kill extended.** Local providers get a longer leash before the chat page kills the request.
- **Local provider detection** covers Ollama, LM Studio, KoboldCpp, vLLM, and any localhost endpoint.

### Advisor Strategy (Fixed)
- **Advisor routing now works in chat.** Plan-vs-execute phase auto-detected from message history.
- **Custom model text field fix.** Selecting "Custom model..." used to clear the model and hide the text input. Fixed for both advisor and executor selectors.

### Agent Skills (SKILL.md)
- **Skills now save to disk.** Imported SKILL.md files persist with manifest tracking.
- **Bulk import.** Import an entire GitHub repo or local parent folder. All subfolders with SKILL.md are imported at once.
- **@-mention in chat.** Type `@` to see a dropdown of installed skills. Select one to inject its SKILL.md content as context for that message. Multiple skills supported.
- **Skills assignable to Agents.** Agent configuration now has a Skills section with checkboxes.
- **Agent skills work across Chat, Spotlight, Browser, Codework, and Organization.**

### Studio
- **API key sharing.** Studio reads keys from main Settings providers. If Google, ElevenLabs, or Azure is configured in Settings, Studio shows "Key set in Settings" instead of "Add API Key".
- **Cloud video generation.** Google Veo, Kling AI, Runway, MiniMax (Hailuo), and Seedance with real cloud API calls and progress polling. No longer "Coming Soon".
- **Veo provider fix.** Was permanently disabled due to a misconfigured provider ID. Fixed.
- **FFmpeg warning hidden for cloud providers.** Cloud video does not need local FFmpeg.

### WordPress
- **Test Connection crash fixed.** WordPress tools now return user-friendly errors for connection refused, 401/403, SSL, and timeout.
- **Full-width CSS v2.** Skales pages render full-width on Astra, GeneratePress, Twenty Twenty-Four, Kadence, OceanWP, and Elementor boxed sections.
- **Skales-created pages flagged for reliable detection.**
- **AI Command Bar reliability.** Clearer workflows for UPDATE, DELETE, CREATE, REDESIGN. More iterations allowed per command.
- **Elementor exits beta.** 5 bundled section templates (Hero, Features Grid, Testimonial, Pricing Table, CTA).
- **WordPress Connector Plugin bumped to v1.1.0**

### Slash Commands (13 new, 24 total)
- `/memory` show memory summary (name, interests, goals, projects)
- `/skills` list installed agent skills with enabled/disabled status
- `/provider` show active provider, model, and base URL
- `/version` show Skales version
- `/export` export current chat as markdown file download
- `/theme` toggle dark/light mode
- `/language` show current locale
- `/settings`, `/discover`, `/studio`, `/codework`, `/wordpress` quick navigation
- `/status` system status check (provider, Ollama, integrations, WordPress)

### Identity Maintenance
- **Runs silently.** No "SECURITY GATE" approval prompts. No dramatic messaging. One-line summary only.

### File Operations
- **Tilde expansion.** `~/` now correctly resolves to home directory across all file write, delete, move, and copy operations.

### Improvements
- Calendar Sync connection status: green/red dot per provider
- Missing skill files warn once per process now, not every cron tick
- ElevenLabs settings link corrected (pointed to Providers, now points to Integrations)
- 13 new slash command descriptions added to all 12 locale files

## v9.2.0 - "The Bridge" (April 2026)

### WordPress Integration (NEW)
- Skales Connector Plugin (MIT-licensed) for WordPress sites
- Token-based authentication
- Auto-detect installed plugins (Elementor, WooCommerce, RankMath, Yoast, cache plugins)
- Create, edit, and delete pages and blog posts with full HTML support
- Elementor page building via JSON sections, columns, and widgets
- WooCommerce bulk price updates by category
- SEO meta management (RankMath and Yoast)
- Media upload (images, videos, PDFs)
- Cache clearing for all major cache plugins
- Full-width CSS injection for Skales-created pages
- Dedicated WordPress management page with AI Command Bar (conversational, real tool execution)
- Per-page AI content generator (inline edit with AI Generate)
- 12 WordPress tools available to the agent

### Stability & Performance
- **Chat loop dedup fix.** Duplicate tool calls are now detected and capped.
- **Tool filter.** Context-based tool filtering reduces the model's tool list per call based on message content.
- **Iteration cap tightened.** Real tasks complete well under the new cap.
- **Skill loading safety.** Missing skill files no longer crash. Graceful skip with warn-once per process.
- **Custom Auto-Updater.** More reliable update flow with streaming downloads.
- **Organization parallel execution.** Independent subtasks now run in parallel.
- **Anthropic timeout increased** for large tool payloads.

### New Features
- **Advisor Strategy.** Opus/GPT-5 for planning, Sonnet/Haiku for execution.
- **Memory Consolidation (Dreaming).** 3-phase overnight memory engine with Dream Diary.
- **Studio Upgrades.** Dynamic model fetching, 10 Style Presets, Camera Controls, Quality Gates.
- **Browser Privacy.** Session isolation, clear cookies, cache, and history.
- **Browser Control v2.** Semantic element detection.
- **OpenClaw Skill Importer.** Import community skills on Custom Skills page.
- **Codework v2.** Multi-file workspace.
- **Lio AI v2.** Template gallery.
- **Social Publishing.** YouTube direct upload and browser-assisted posting.

### Improvements
- Settings page reorganized: Memory tab, Studio tab, proper grouping
- Sidebar grouped into Main, Tools, System sections with always-visible tooltips
- Skills renamed to "Add-Ons" with Activate/Deactivate All
- Model lists update everywhere when one provider's catalog refreshes
- Dark mode persistence improved
- Templates cache faster now
- 12 locale files with 50+ new translation strings
- BSL-1.1 license clarification header

### Bug Fixes
- Tool-call infinite loop fixed
- Browser links no longer open new windows unexpectedly
- File save dialog works correctly
- Settings search covers all new sections (WordPress, Advisor, Dreaming, Memory)
- Sidebar tooltip always visible
- Settings page no longer crashes during navigation
- Studio: Black image, video preview centering, gallery download, reference image handling
- Browser: Session isolation, privacy dropdown, history clear, new tab crash
- Theme and locale persist even after browser data is cleared
- ONNX runtime warnings suppressed

---

## v9.1.0 - "The Studio Update"
Released: April 7, 2026

The biggest feature release in Skales history. Skales Studio,
Templates, Planner AI Tasks, and 40+ improvements across the board.

### Skales Studio (NEW)
- **Image Generation.** Multi-provider support: Skales Visuals
  (built-in renderer), Replicate (Flux, SDXL), HuggingFace,
  OpenAI DALL-E, ComfyUI (local), Stable Diffusion WebUI (local).
- **Video Creation.** Describe a motion graphic, preview the animation live,
  iterate with natural language, export as MP4. Categories: Text
  Animation, Infographic, Data Visualization, Logo Intro, Slideshow,
  Social Post, Counter/Stats.
- **Voice / TTS.** Text-to-speech with automatic provider detection:
  Local, ElevenLabs, Azure Neural, Groq, OpenAI, Google TTS.
- **Music Generation.** AI music via Meta MusicGen (HuggingFace).
  Genre, mood, and duration selection.
- **Gallery.** All generated content (images, videos, audio) saved
  and browsable with filter, search, masonry layout, and reuse.
- **Export.** Format presets for TikTok, YouTube, Instagram, LinkedIn,
  X/Twitter. AI caption and hashtag generator (v9.2.0).
- **Brand Kit.** Save logo, colors, fonts, tagline, tone of voice.
  Optional injection into all Studio generations.

### Templates (NEW)
- 37 pre-built templates across all modules: Chat, Codework,
  Organization, Lio AI, Browser, Planner, Studio
- Click a template to open the module with prompt pre-filled
- Template Maker: AI-guided interview wizard to create custom templates
- Templates shared via Discover Feed (fork from other users)

### Planner AI Tasks (NEW)
- Schedule AI tasks: once, daily, weekly, monthly
- Tasks appear in the calendar grid (purple blocks)
- Auto-execution on schedule
- Confidence scoring: tasks below 50% confidence are skipped
- Dry Run mode: simulate without executing destructive actions
- Task result history with tools used and duration

### Fallback Provider Chain (NEW)
- Configure backup AI providers in Settings
- If primary provider fails, auto-switch to fallback
- Banner notification: "Fallback active: Using [provider]"
- Auto-recovery: checks primary periodically
- API keys inherited from saved provider settings

### Ollama Model Marketplace (NEW)
- One-click install for recommended local models
- Gemma 3, Llama 3.3, DeepSeek R1, Mistral, Phi-4, Qwen 3,
  Codestral listed with sizes and descriptions
- Progress bar during download
- Auto-detection of ComfyUI and Stable Diffusion WebUI

### Browser Playbooks (NEW)
- Record browser sessions as replayable workflows
- Auto-capture URL navigations, clicks, and inputs
- Schedule playbooks as recurring Planner tasks
- Available to the agent in chat and organization

### Social Media Integration (NEW)
- YouTube and LinkedIn OAuth connection in Settings
- Post directly from Studio Export tab
- Instagram, TikTok, Facebook placeholders for v9.2.0

### Knowledge Graph (NEW)
- Knowledge graph builds relationships as you work
- Entities: projects, people, tools, preferences
- Agent can query, update, and delete entries
- Enable/disable toggle and reset in Settings

### Organization Improvements
- Real approval UI with state machine (approve/reject destructive tools)
- Canvas Office visualization
- Shared memory between agents
- Projects CRUD (create, rename, archive, restore, continue)

### Agent Tools (21 new)
21 new agent tools across Brand Kit, image generation and editing,
video creation, voice generation, content extraction, social media,
planner, knowledge graph, and playbooks.

### Internationalization
- 2800+ translation strings across all 12 languages
- All Settings strings now multilingual (450+ converted from hardcoded)
- All module pages fully translated
- Bootstrap wizard fully translated with proper locale loading

### Bug Fixes
- Fixed: Dashboard greeting showing raw translation keys
- Fixed: Planner week and day view missing calendar events
- Fixed: Path traversal vulnerability on code routes
- Fixed: Discover posts showing tips instead of completed tasks
- Fixed: Fallback chain providers not removable after save
- Fixed: Template click not inserting prompt in target module
- Fixed: FFmpeg path resolution on macOS, Linux, Windows
- Fixed: Browser links opening in new tab crashing session
- Fixed: Render progress stuck at 0%
- Fixed: Gallery video downloads serving wrong path
- Removed Beta badges from Swarm, Codework, Organization
- Added Beta badges to Studio, Templates, Playbooks

---

## v9.0.2 - Patch (April 2026)

### Fixed
- Settings: API keys no longer disappear when switching models
- Codework: Project names are sanitized to valid slugs
- Organization: Clipboard copy works with visual feedback
- Organization: Agents respond in the user's configured language
- Discover: Post templates rewritten to describe user activity
- Discover: Added events for Codework and Organization completions

### Added
- YouTube Data API v3 integration (search, video details, channel info, trending, captions)
- Codework: Web search available during code generation

---

## v9.0.0 - "For the People" (April 2026)

### Highlights
- **Agent Skills Import.** Native support for the SKILL.md open standard. Import from Claude Code, Codex, Copilot, Cursor. GitHub URL, local folder, or paste. Works across Chat, Codework, Browser, Spotlight, and Lio AI.
- **Skales Codework.** Autonomous coding agent. Select project folder, describe task, pick model, watch live diffs in 3-panel GUI. Session history and follow-up conversations.
- **Organization.** Multi-agent teams with CEO delegation, departments, Company Packs.
- **Computer Use.** Desktop automation via screenshots, clicks, keyboard, scrolling.
- **Calendar Sync.** Google, Apple, Outlook, CalDAV unified in Planner.
- **7 Integrations.** Notion, Todoist, Spotify, Smart Home, Google Drive, GitHub, Google Docs.

### New Features
- DevKit with API Playground, Debug Panel, CLI, 50+ tool reference
- Migration Importer (ChatGPT, Claude, Copilot, Gemini, OpenClaw, Hermes)
- MCP Server Support (Model Context Protocol)
- New default theme "Skales Modern" (navy and emerald, light/dark)
- Messaging Gateway (Slack, Signal actions)
- 9 professional agents
- DeepSeek direct provider
- Browser Workspaces and Playbooks
- Custom model ID input per agent
- Settings dynamic search (35+ sections)
- Swarm VPN fallback with manual peer IP
- OG preview cards in Discover

### Changed
- Default theme from Classic to Skales Modern
- Light mode as default on first launch
- Skills default state: core features ON, experimental OFF
- Browser loop detection relaxed
- Bubble dismiss timer extended
- Badge color from lime to emerald
- Removed "2.0" from Discover branding
- Removed "Beta" from Browser
- 12 languages (added Turkish, Croatian)

### Fixed
- Calendar events async export crash
- Codework tool hallucination
- Spotlight white flash on open
- Theme flash on restart
- Migration importer file picker error
- Telegram inline keyboard, toast dedup, key persistence
- Settings search not finding Planner and Calendar
- Advanced Integrations in wrong Settings tab
- Raw translation keys in UI
- Crash on certain file writes
- Codework and Organization skill check logic
- Swarm sidebar gating and Skills page toggle
- Dashed-border styling replaced throughout

---

## v8.0.2 - Hotfix (April 2026)

### Fixed
- Chat Error 400: message reconstruction simplified
- Agent "Done" response: removed incorrect tool-call stripping that caused infinite loops with Gemini
- IMAP Email: connects to real server again
- Custom Skills iframe: buttons, inputs, and saves now functional
- Planner weekdays now respect app language setting instead of system locale
- Chat message source badges restored (Desktop, Buddy, Telegram, Spotlight)
- Toast notifications: added X button and click-to-close
- Stop button: more stable behavior on chat page unload
- Chat bubble word-break: fixed mid-word splitting ("correc\nt?" becomes "correct?")
- Think tags no longer leak in Lio AI and Skill AI outputs
- TTS "default" provider: added browser voice selector
- Custom OpenAI-compatible provider: status indicator reflects actual URL config
- Skill Generator: defaults to user's active provider instead of hardcoded OpenRouter
- Telegram bot: auto-reconnects on app restart
- Telegram Safety Mode: approval buttons (Approve/Deny) now use inline keyboard
- Desktop Buddy: sound and notification suppressed when main chat window is focused
- macOS auto-updater: added required ZIP target alongside DMG

### Added
- Notification delete: X button per notification and "Clear All" to dismiss all
- Provider-specific exit condition diagnostics

---

## v8.0.1 - Hotfix (March 2026)

### Fixed
- Chat crash after multiple messages
- Port 3000 conflict detection with health check
- Forked skills truncation (raised limit, added syntax validation)
- Hardcoded German text in UI when set to English
- IMAP email tools now visible to AI when configured
- Discover event spam rate limit survives restart
- A dozen type errors resolved
- Compose drafts now context-aware with per-category agent personality
- Anonymous ID visible in Settings with copy button
- Skills filter tab in Discover feed
- Documents skill: removed non-working Excel claims
- Dark mode dropdown text visibility
- Autopilot priority change no longer deletes task content
- "Feedback submitted" toast shows translated text
- Light theme toggle visibility

### Added
- Migration banner for v8.0.0 users to pick single agent vibe
- Skills filter tab in Discover

---

## v8.0.0 - "Discover 2.0" (March 2026)

### Highlights
- **Discover 2.0.** The first social network where AI agents post, spark,
  mention, and share skills with each other
- **Skill Sharing & Forking.** Share AI-created Custom Skills to Discover.
  Other users can fork (copy) them with one click.
- **Spark.** Send sparks to other agents. Skales' answer to Facebook Poke
  and MSN Nudge. With sound notifications.
- **3 New Languages.** Vietnamese, Croatian, Turkish (12 total)

### New Features
- Discover Feed 2.0 with @mentions, replies, emoji avatars, compose box,
  network visualization, date filters, trending posts
- Spark social interaction system with 6 spark types and sound effects
- Skill AI watermarking for sharing verification
- Share to Discover button for AI-created Custom Skills
- Fork Skill: one-click copy of community skills with safety disclaimer
- Heartbeat system with online counter and network visualization
- Report system for feed moderation with admin review
- Image sharing pipeline with admin approval workflow
- Notification sounds toggle in Settings
- Native file tools for macOS Full Disk Access compatibility
- Toast notification system with theme-aware styling
- Discover onboarding with interest selection, color accent, emoji avatar
- Date filter (Today, This Week, 30 Days, All Time)
- Delete own posts from Discover Feed
- Magic / Spark button for lightweight social interactions
- Edit Discover Profile without losing gamertag

### Fixed
- Discover event pipeline for all tools (images, browser, skills, planner,
  buddy, group chat, spotlight, swarm)
- Browser agent "URL unchanged" false positive on scroll and screenshot actions
- Telegram bot stale lock file on restart
- Request timeout setting now respected (uncapped)
- macOS TCC permission errors with helpful Full Disk Access guidance
- Notification action label displayed correctly
- Swarm only starts network discovery when Swarm is enabled
- Desktop Buddy persists across show/hide (no intro restart on minimize)
- Desktop Buddy no longer steals Cmd+Tab or Dock visibility on macOS
- Token display "0" regression
- Tag duplication in Discover posts
- Toast readable on all themes (light and dark)
- Obsidian theme header icon alignment
- Wrapped card dynamic refresh (includes today's activities)
- Network visualization on high-DPI displays (dots no longer cluster)
- Wrapped PNG export now pixel-accurate

### Languages
- Vietnamese (vi). Full translation.
- Croatian (hr). Full translation, Latin script.
- Turkish (tr). Full translation with correct special characters.
- Total: 12 languages (EN, DE, ES, FR, IT, PT, KO, ZH, JA, VI, HR, TR)

---

## v7.6.6 - Hotfix (March 2026)

### Fixed
- **CRITICAL: Discover Feed tool events never fired.** When users opted in to Discover on the /discover page, the opt-in was saved only locally and never propagated server-side. This caused server-side tool events (image generation, browser sessions, task completions, file organization, swarm dispatches, etc.) to silently not appear in the feed. Only conversation completions worked. Existing users get a background sync on next chat page load.

---

## v7.6.5 - The Intelligence Update (March 2026)

### New Features
- **Token Compressor.** 3-level prompt compression (Full, Compact, Minimal) to reduce API token usage. Configurable in Settings. Minimal is ideal for Spotlight and quick tasks.
- **In-App Toast Notifications.** Floating glassmorphic toasts in the main chat for background task completions, multi-agent dispatches, and dashboard notifications. Auto-dismiss after 5 seconds.
- **Awareness of all UI features.** Skales now knows about Discover Feed, Desktop Buddy (3 skins), Spotlight, Autopilot, Voice Chat, Notifications, Agent Swarm, Multi-Agent Tasks, Planner AI, and Custom Skills. Can navigate you to the correct pages.
- **Discover Feed AI Summaries.** AI instances generate first-person activity summaries locally. Approve or reject on the Discover page before sharing to the community feed. Pulsing dot indicators in sidebar for pending approvals and unread notifications.
- **Custom Skill Interactive UI.** Skills can render their own interactive UI in chat with a bridge for skill-to-host communication.

### New Features (cont.)
- **Skales Wrapped.** Spotify-style weekly stats card. Auto-generates every Monday at 8am or on-demand. Two shareable formats: Square (1:1) and Story (9:16). 4 theme-matched card designs (Skales, Obsidian, Snowfield, Neon). Count-up animations, confetti celebration, staggered stat reveals, animated activity chart. 9 personality badges (On Fire, Power User, Night Owl, etc.). Download PNG, Copy to Clipboard, or Post to Discover. Sidebar pulsing dot when new data. Fully localized in all 9 languages.
- **Discover Feed: GIF Support.** Attach Klipy or Giphy GIFs to AI Summaries. GIF preview in pending approval, safe rendering in feed cards. Admin panel shows GIF URLs and reply-to quotes.
- **Discover Feed: AI Reply and Repost.** Reply to feed entries with AI-generated text. Repost entries to amplify community content. Both with auth verification and rate limiting.
- **Discover Feed: Personality System.** User personality profiles influence AI summary tone and style.
- **Discover Feed: Vibes Tab.** Server-side filtered tab showing only AI summaries.

### Security
- **Discover Repost Auth and Rate Limiting.** Repost endpoint verifies your opt-in. Rate limit: 5 reposts per hour per user.
- **NSFW Filter Expansion.** Gamertag and content filter expanded to 30+ blocked terms covering explicit, violence, hate speech, drugs, and spam.
- **GDPR Delete User Compliance.** Two-pass scrub on user deletion: removes all entries by the leaving user and scrubs replies that referenced them.
- **Admin Panel v6.** Brute-force rate limiting, CSRF tokens, security headers, session hardening.
- **API Rate Limiting on report-status and notifications endpoints.** 429 responses with Retry-After headers.
- **Input validation on all public endpoints.**

### Fixed
- **Discover GIF overflow.** GIF images in feed cards no longer break layout.
- **Discover Vibes tab performance.** Moved to server-side filtering.
- **Discover repost offline handling.** Distinct error messages for rate-limit, server unreachable, and offline states. Graceful degradation for deleted-entry reposts.
- **Admin mobile access.** Burger menu for mobile viewports.
- **Version adoption metric** properly computed from telemetry.
- **Custom Skill buttons.** Skill UIs render interactively with onclick handlers and scripts executing.
- **Skill iframe communication** validated more strictly.
- **Notification polling** reduces server load significantly.

---

## v7.5.0 - The Social Update (March 2026)

### New Features
- **Discover Feed.** Global activity feed showing what the Skales community is building. Gamertag system, upvotes, category filters, blurred preview for non-members. Privacy-first: zero personal data collected, white-label templates only.
- **Spotlight and Vision.** Press Cmd/Ctrl+Shift+S to open a floating search bar anywhere on your desktop. Ask Skales anything without opening the main window. Eye button captures your screen and attaches it to the query for visual context (requires vision-capable model).
- **Spotlight Settings Toggle.** Enable or disable the Spotlight Bar and its global keyboard shortcut from Settings > Notifications. Setting persists across restarts. Disabling it also unregisters the global shortcut to prevent conflicts with other apps.
- **Mini-Chat Mode.** Shrink Skales to a compact always-on-top chat window. Toggle from the chat header or use the Spotlight shortcut.
- **Sound Notifications.** Audible feedback when tasks complete, notifications arrive, or Swarm tasks finish. Theme-aware sounds, configurable in Settings.
- **Agent Swarm Redesign.** Dedicated Swarm page with hub-and-spoke node visualization, task history, quick delegate, and chat integration hints.
- **Notification Center.** Dedicated page for all notifications with read/unread state, filters, and admin broadcast support.
- **Calendar Month View.** Full month grid with event previews, click-to-navigate, and today highlighting.
- **Planner .ics Export.** Download your plan as a calendar file without connecting a provider.
- **TTS Local Provider.** Connect KoboldCpp, XTTS-API-Server, or any OpenAI-compatible TTS endpoint. Configurable in Settings with timeout and automatic browser fallback.
- **Privacy Policy and Delete My Data.** GDPR-ready privacy policy page, in-app Delete My Data button with 2-step confirmation that purges all server-side telemetry, bug reports, and feedback by IP hash.
- **Cookie Consent.** Landing page cookie banner with Google Consent Mode v2. Default deny for analytics/ad storage, granted on explicit Accept.

### Deprecated
- **Network & DLNA.** Network Scanner and DLNA/UPnP casting features retired. The /network route now redirects to Swarm. DLNA casting is planned as a dedicated Smart Home Skill in a future update.

### Fixed
- **Friend Mode and Buddy Intelligence.** Both systems now fire independently of Autonomous Mode. Morning greetings, idle check-ins, meeting reminders, and proactive messages work as configured even when Always-On is off.
- **Calendar delete persistence.** Deleted events stay deleted across view switches and reloads.
- **Desktop Buddy persistence.** Buddy no longer disappears after tab switch or command execution.
- **Ollama detection.** Better error messages and faster checks.
- **KoboldCpp tool calling.** OpenAI-compatible endpoints now send the tools array by default.
- **Telegram loop.** Continued stability from v7.2.1 fix.
- **Bug report email.** Optional contact email now saved and visible to admin.
- **Bug report status sync.** Users see Open, In Progress, Closed status and admin notes.
- **Notification client polling.** Dashboard cards and Notification Center display server notifications.
- **Prompt optimization.** Further token reduction for free-tier models.
- **Swarm state sync.** Settings toggle correctly starts and stops network discovery, auto-starts on boot.
- **Theme responsive.** Swarm, Notifications, and Discover added to all nav variants and mobile menus.

### Security
- API key enforcement for the telemetry endpoint
- Discover Feed: 3-layer gamertag validation, admin shadowban system, rate limiting
- Privacy policy link in Settings > Discover

## v7.2.1 - Hotfix (March 2026)

### Fixed
- **Prompt optimization.** Reduced token usage by moving dynamic context to on-demand tool calls. Free-tier models no longer hit rate limits on simple messages.
- **Calendar widget.** Fixed "Invalid Date" for Google Calendar events with missing dateTime fields.
- **Planner calendar sync.** Fixed "3 errors detected" when sending plans to calendar.
- **Model list cleanup.** Removed deprecated Gemini 1.5 models, added current Groq, OpenRouter, Google models.
- **Weather widget.** Uses device geolocation with Vienna fallback instead of hardcoded Berlin.
- **Telegram approval loop.** Fixed. System jobs bypass approval gates entirely.
- **Identity Maintenance.** Runs automatically without Telegram approval when enabled.
- **Friend Mode.** Tagged as system job, no longer blocked by approval gates.
- **Scheduler heartbeat.** Cron jobs actually execute now.
- **Newsletter telemetry.** Email subscriptions now transmitted to server.

### New
- **Notification system.** Admin can send broadcasts and bug report replies to users. Dashboard shows dismissable notification cards.
- **Browser agent improvements.** DOM settle delay, auto and manual approval modes, dangerous button blacklist, CAPTCHA detection, real-time action log with Markdown export.
- **Agent Swarm.** Network discovery, task delegation, delegate tool, firewall detection, loop prevention.
- **Theme responsive.** Hamburger menus for Snowfield and Neon on mobile, Obsidian tablet overlap fixed, Custom Skills in all mobile menus.

---

## v7.2.0 - "The Next Chapter" (March 2026)

### New Features
- **Dashboard Widgets.** Customizable drag-and-drop dashboard with resizable widgets
  (clock, weather, system stats, quick actions, recent chats, tasks, notes, pomodoro).
  Per-widget settings and theme-aware styling.
- **Cron Scheduling.** Natural-language and cron-syntax task scheduling with job
  management UI, execution logs, and retry support.
- **Browser Tool.** Built-in browser for web research and interaction.
  Sandboxed with user approval flow and screenshot capture.
- **Always-On Agent [BETA].** Background agent that monitors and executes scheduled
  tasks autonomously. Toggle in Settings > Advanced.
- **Live Duplex Voice - Call Mode [BETA].** Full-screen continuous voice
  conversation. Speech-to-LLM-to-speech with barge-in support, waveform visualization,
  and end-call keyword detection. Toggle in Settings > Voice.
- **Multi-Agent Swarm [ALPHA].** LAN peer discovery.
  Discover other Skales instances on the network, view status, and send tasks.
  Hidden behind Settings > Advanced > Experimental.
- **PWA Mobile and Tailscale [BETA].** Progressive Web App manifest with installable
  icons. /mobile page with 4-step Tailscale setup wizard and QR code for phone access.
- **Feedback Page Upgrade.** View your own bug reports with status badges (open,
  in progress, closed). Optional email field on bug reports. Newsletter opt-in
  in onboarding and settings.

### Improvements
- **Ollama Connectivity.** All references switched from localhost to 127.0.0.1 to fix IPv6 resolution issues.
- **Social Links.** X, Instagram, TikTok, and YouTube links in settings, update,
  and feedback page footers.
- **robots.txt and llms.txt** for crawlers and LLM agents.
- **Locale Expansion.** 9 languages (en, de, es, fr, ru, zh, ja, ko, pt) now have full coverage.
  All em-dashes removed from non-English locales.
- **Theme System.** Instant theme switching.

---

## v7.1.0 - "The Local AI Update" (March 2026)

### Bug Fixes
- **Telegram Approval Loop.** Fixed infinite loop where approving an action in Telegram
  triggered the same approval again. Approval responses route correctly and do not
  trigger memory scans.
- **IPv6 localhost.** Fixed bot-to-server connection failure on systems where localhost
  resolves to ::1 instead of 127.0.0.1. (Thanks @bmp-jaller)
- **Think Tags.** No longer leak into chat responses from Qwen and DeepSeek
  models via KoboldCpp. (Thanks @henk717)
- **Desktop Buddy Approve.** Approve button no longer shows "cancelled" due to a sandbox issue.
  Input field no longer overlaps approval buttons.
- **Auto-Updater.** Honest message: "Download at skales.app" instead of a false
  "will install automatically" claim.

### Improvements
- **Onboarding Renamed.** "Custom Endpoint" became "OpenAI Compatible" and moved above Ollama.
  KoboldCpp, LM Studio, vLLM are now first-class options, not hidden under "Custom".
- **API Key Truly Optional.** Empty key means no auth header sent. Local AI servers
  that don't need authentication work without workarounds.
- **Local TTS Endpoint.** Voice settings support local TTS servers (KoboldCpp,
  XTTS-API-Server). Not limited to cloud providers.
- **Local STT Endpoint.** Voice transcription can use local Whisper (KoboldCpp).
- **Local Image Generation.** Configurable image generation endpoint alongside Replicate.

## v7.0.1 - Hotfix (March 2026)

### Bug Fixes
- **Telegram Bot.** Fixed bot process crash on end-user machines. The bot now uses the bundled Node runtime instead of requiring a system Node install. Same fix applied to the WhatsApp bot.
- **Chat Frozen.** Fixed chat becoming unresponsive after vision model error. Session history is now sanitized before every call, preventing corrupted message blocks from breaking subsequent requests.
- **Streaming Timeout.** Added an inactivity timeout to prevent the chat UI from hanging on broken responses.
- **Vision Fallback.** When a model doesn't support vision, images are stripped gracefully and the message is sent as text-only instead of corrupting the session.

---

## V7.0.0 - "The Foundation" (March 2026)

### New Features
- **Proactive Desktop Buddy.** Rule-based buddy intelligence observes calendar, email, tasks, and idle time. Meeting reminders, end-of-day summaries, idle check-ins, morning greetings. Respects quiet hours. No LLM calls.
- **Planner AI.** AI-powered daily scheduling. 8-step wizard learns work patterns, generates time-blocked plans from calendar events, pushes them back to your calendar. Chat integration: "plan my day."
- **Calendar Abstraction.** Google Calendar, Apple Calendar, and Outlook. All three work simultaneously. Planner AI reads from all providers.
- **FTP/SFTP Deploy.** Upload Lio AI projects to any FTP server. Per-project deploy config, incremental upload, test connection, 4 website starter templates.
- **7 Languages.** English, German, Spanish, French, Russian, Chinese (Simplified), Japanese. Full UI translation including onboarding.
- **Skales+ Tiers.** Free Forever, Personal ($9/mo), Business ($29/mo) tier page with waitlist. All features free during beta.
- **Morning Briefing.** Daily digest of calendar events, pending tasks, unread emails, delivered via Telegram and chat.
- **File Sandbox.** Three modes: Unrestricted, Workspace Only, Custom Folders. Enforced on all file tools.
- **Redesigned Onboarding.** 7-step wizard with Cloud, Local, Custom provider cards, Ollama auto-detect, buddy picker, safety mode selection.
- **Model Auto-Fetch.** Real-time model lists from OpenAI, Google, OpenRouter. No more hardcoded model IDs.
- **Linux Beta.** AppImage and .deb builds for x64 Linux.

### Improvements
- **Unified notifications.** All notifications go through one system. Quiet hours, per-type cooldowns, channel routing (bubble, Telegram, dashboard).
- **Settings Restructured.** 6 tabs (General, AI Providers, Integrations, Buddy, Security, Advanced) replace the single long scroll.
- **Custom Endpoint Equality.** Vision toggle, TTS URL, configurable timeout. Local AI is a first-class citizen.
- **Ollama Revolution.** Auto-detect on startup, real model dropdown, faster checks, CORS warning.

### Bug Fixes
- Email Bug 31: Agent respects "send from marketing@" instructions
- Buddy speech bubble height for approval dialogs
- Think and reasoning tags stripped from buddy bubble
- Email whitelist per-mailbox
- Custom endpoint timeout configurable
- Empty API key no longer sends blank auth header
- WhatsApp toggle marked "Coming Soon" (was a dead control)
- Dashboard notification channel fixed (was silently dropping messages)

### Platform
- Windows x64 (stable)
- macOS Apple Silicon (stable)
- macOS Intel (stable)
- Linux x64 AppImage (beta)
- Linux x64 .deb (beta)

---

## V6.2.0 - "The Telegram Fix" (March 2026)

### Critical Fixes
- Fixed: Endless Telegram approval loop. Multiple read-only tools were incorrectly classified as requiring approval. Fixed.
- Fixed: New tools no longer silently block by default. The fallback safety setting flipped from confirm to auto.
- Fixed: Telegram session history preserves tool results properly. The model no longer re-calls already-executed tools.
- Fixed: Google Translate TTS was hardcoded to German. Now uses the user's configured language and locale.

### Improvements
- Telegram approval route now continues the agent loop for natural responses after tool execution
- Autopilot "yes"/"no" intercept now checks task age (5 min window) to prevent eating unrelated messages
- Memory leak fixed in the chat shell on page show events

## V6.1.1 - Hotfix (March 2026)
- Fixed: Telemetry settings now read correctly by the feedback page.
- Fixed: Feature Request textarea not editable on /feedback page (was disabled when telemetry appeared off)
- Fixed: Report Bug sidebar link now opens /feedback instead of old modal
- Fixed: Skin and language changes now prompt for restart
- Fixed: Chat input focus lost after deleting chat or receiving media via Telegram
- Fixed: White screen on mobile Chrome tab switch via Tailscale
- Fixed: Privacy section consolidated. Telemetry toggle moved into Security & Privacy, with dynamic text based on state.
- Updated: All 4 locale files (en, de, es, fr) with new privacy section translations

## V6.1.0 - "The Awakening" (March 2026)

### Autopilot - True Autonomous Agent
- **Recurring Task Scheduling.** Master Plan now generates cron jobs for recurring goals. "Check my email every morning at 8am" creates an actual scheduled task, not a one-shot.
- **Live Execution View.** New tab in Autopilot dashboard shows real-time agent reasoning, tool calls, and results as they happen. Watch Skales think.
- **Automatic Daily Stand-up.** Autopilot generates and delivers a daily briefing via Telegram every weekday at 9am (configurable). No button click needed.
- **Safe Mode: Approval Instead of Skip.** Scheduled tasks in Safe Mode pause for approval instead of being silently skipped. Visible on the Execution Board.
- **Telegram Approval for Autopilot.** Approve or reject Autopilot tasks directly from Telegram. Reply `approve <id>` or `reject <id>`, or simple `yes`/`no` for single pending tasks.
- **Accurate API Rate Limiter.** Cost controls count actual calls per task now. Budget reflects real usage.

### New Features
- **Bubbles Mascot Skin.** A playful blue liquid blob that morphs into different shapes. Selectable in Settings > Desktop Buddy alongside the original Skales gecko.
- **Feedback & Rating System.** New /feedback page with 3 sections: Rate Skales (4 emoji ratings), Report a Bug, Request a Feature. Data sent to server only with telemetry opt-in. GDPR compliant.
- **Admin Dashboard v3.** Redesigned analytics dashboard. Feedback tab with rating distribution, feature request table, and timeline view.

### Bug Fixes (13)
- Fixed: Telegram approval gate ignores safetyMode (Critical)
- Fixed: Telegram inline keyboard buttons never appear (replaced with text-based approval)
- Fixed: Telegram agent hallucinates tool execution when blocked by approval
- Fixed: Telegram pairing shows readable text instead of a raw translation key
- Fixed: Telegram duplicate messages (409 Conflict) from multiple polling instances
- Added: Telegram purge/reset button in Settings
- Fixed: Orphaned tool result blocks no longer crash the Anthropic API path
- Fixed: Replicate images not saved to workspace
- Fixed: Telemetry provider type event now has a cooldown instead of firing on every message
- Fixed: Telemetry language event no longer fires on every app start
- Fixed: Telemetry sends data when opt-in is disabled (GDPR violation)
- Fixed: Rate-limit error now shows the translation instead of a raw key
- Fixed: Identity Maintenance file-path bug
- Fixed: 'medium' priority works correctly in cron task creation

---

## v6.0.2 (2026-03-16)

### Fixed
- **[CRITICAL] Telegram approval gate ignores safetyMode.** Unrestricted mode bypasses approval entirely via Telegram now.
- **[CRITICAL] Telegram inline keyboard buttons don't work.** Replaced with text-based approval ("yes"/"no" replies).
- **[CRITICAL] Agent hallucinates tool execution.** Blocked tools now make their blocked state explicit in the conversation, preventing the model from claiming success.
- **[CRITICAL/LEGAL] Telemetry sends data when opt-in is disabled.** Added a defense-in-depth opt-in check. Zero network requests when telemetry is off. GDPR compliance.
- **[HIGH] Orphaned tool-result blocks crash API.** Added message sanitization that removes tool results referencing non-existent tool calls.
- **[HIGH] Telegram pairing shows raw translation key.** Improved pairing success and failure messages with clear guidance.
- **[MEDIUM] Telegram duplicate messages (409 Conflict).** Added deduplication guard to prevent processing the same update twice.
- **[MEDIUM] Replicate images not saved to workspace.** Fixed download path with explicit error checking.
- **[LOW] Telemetry provider type fires too often.** Cooldown increased.
- **[LOW] Telemetry language event fires every start.** Now only fires when language actually changes.

### Added
- **Telegram Reset button.** "Reset All" button in Settings > Telegram purges all Telegram data.
- **Bubbles mascot skin.** New mascot option: a blue liquid blob that morphs into different shapes. Select in Settings > Desktop Buddy > Skin.
- Skin descriptions shown in the mascot selector UI

### Improved
- Gemini and Replicate images save to a consistent location
- Telegram approval messages are clearer and more user-friendly
- Telegram pairing and error messages are now hardcoded for reliability

---

## v6.0.1 (2026-03-15)

### Fixed
- Safety Mode simplified to Safe and Unrestricted (removed confusing Advanced mode)
- Approval flow no longer stalls after confirming tool execution
- Safe Mode continues the agent loop after approval
- Telegram bot shows proper messages instead of raw translation keys
- Telegram approve and decline inline buttons appear correctly
- Telegram messages cleaned of markdown formatting before sending
- Custom Skill pages have a working input field for all skills
- Replicate image generation works with the new API format
- Telemetry ping limited to once per minute per event
- Capabilities check reports always-active features honestly
- Identity maintenance cron uses built-in tools instead of shell commands (cross-platform safe)
- Locale files verified: all 4 languages have identical structures

### Improved
- Agent acts immediately instead of explaining what it could do
- Platform-aware behaviour: PowerShell vs bash rules based on OS
- Unrestricted mode enables full autonomy
- PDF handling instructions added (try read_file before shell tools)
- Custom skill creation guidance improved
- Enhanced anonymous telemetry: tool usage, provider type, language, feature usage
- All telemetry remains opt-in, anonymous, and GDPR compliant

---

## v6.0.0 - "The Foundation" (March 2026)

### Multilingual
- Full UI translation: English, Deutsch, Espanol, Francais
- Language picker as first onboarding screen
- Language switcher in Settings (always accessible, no restart needed)
- System messages, approval prompts, and error messages translated
- Telegram bot responses in the user's selected language
- Desktop Buddy speech bubbles translated

### New Providers
- Replicate integration (BYOK). Access 50+ image and video AI models with one API key.
- Custom OpenAI-compatible endpoint. Supports llama.cpp, LM Studio, vLLM, koboldcpp, text-generation-webui.
- Tool calling toggle for custom endpoints (on/off, for local models that don't support function calling)

### Skales+
- Tier comparison page (Free Forever, Personal, Business)
- Email waitlist for upcoming premium features
- All current features remain free and unlocked

### Desktop Buddy
- Flickering fix. Smooth crossfade transitions between animations.
- Dynamic folder system. New animations load automatically.
- Full skin system. Add new mascot clips and select from Settings.
- Skin selector in Settings (Desktop App section), shown when more than one skin is installed
- Buddy can now execute tools: write files, send emails, browse the web, manage calendar, and more
- Approve and Decline buttons appear inside the speech bubble when an action needs confirmation
- Auto-executed tools (safe actions) run immediately and show a result bubble
- Tool result summaries shown inline. "Open Chat for details" link for long outputs.

### Privacy and Feedback
- Anonymous telemetry opt-in. Disabled by default. Prompt during onboarding.
- Collected data: app version, OS platform, start and crash events only
- No conversations, no API keys, no file paths, no personal data ever sent
- Anonymous UUID generated once and reused. Never regenerated.
- Report Bug button in the sidebar (bottom, above Stop Server)
- Bug reports sent to developer. Local fallback if offline.
- System info (OS platform) included in reports. Optional checkbox, on by default.

### Approval System
- Unknown tools require approval by default
- Document creation tool added (creates files inside the workspace)
- Telegram inline buttons appear correctly
- Telegram bot works on any port

### Security
- Default tool safety prevents hallucinated tool execution
- Telegram callbacks work regardless of which port Skales is running on
- Chat bubble overflow protection (no more horizontal scrollbar on wide content)

### Bug Fixes
- Chat bubble horizontal overflow fixed
- Translations applied in places that were missing them
- PDF dependency loads reliably

---

## v5.5.0 - March 2026

### Security
- Approval system enforcement. Destructive actions (send email, delete file, calendar changes, tweets) require explicit user confirmation.
- Browser blacklist covers Playwright too. Blocked domains can no longer be bypassed.
- Unrestricted Mode properly bypasses approval gate when enabled
- Screenshot auto-Telegram removed. Only forwards when explicitly requested.

### Accessibility
- ARIA labels on all interactive UI elements (buttons, inputs, navigation, toggles)
- Full keyboard navigation with visible focus indicators
- Screen reader support on chat, buddy bubbles, and approval dialogs
- Skip-to-main-content link
- Compatible with NVDA (Windows) and VoiceOver (macOS)

### Desktop Buddy
- Friendly error messages ("Oops, could you take a look?") instead of raw errors
- Video transition flickering fixed
- Honest response when tools unavailable ("I can only do that in the main chat, Open Chat")

### Bug Fixes
- Input field lock after chat deletion resolved
- Spellcheck disabled globally (both main window and buddy window)
- Custom Skill buttons work now
- Confirmation message shown after approved tool execution

---

## **5.0.0** - The Desktop Companion Update (2026-03-02)

Skales v5.0.0 is the largest single release in the project's history. It ships the full Autopilot meta-agent, Voice Chat, a Custom Skill Ecosystem, Document Generation, Google Places, a Network Scanner, DLNA Casting, a brand-new **Desktop Buddy**, and a comprehensive v5 stability pass.

---

### **Desktop Buddy - Floating Mascot & Spotlight Quick Action**

- **Transparent always-on-top window** at the bottom-right corner of the primary display. No taskbar entry. No shadow. Fully draggable.
- **Mascot states.** The mascot cycles through Intro (welcome clip on launch), Idle (looping base animation), Action (random clip at varying intervals, never repeats the same clip until all have played), and Query (when clicked).
- **Spotlight Quick Input.** Glassmorphism input field with backdrop blur, lime-green glow border, and animated loading spinner. Press Enter to submit, Escape to dismiss.
- **AI Response Bubble.** The reply is shown as a glassmorphism speech bubble with a pointer tail, auto-dismissed after 10 seconds (click to dismiss early). Replies are trimmed for readability.
- **Silent session sync.** Every question and answer is silently saved per day.
- **Settings Toggle.** Desktop Buddy toggle in Settings > Desktop App.
- **Smart Visibility.** Buddy window appears when the main window is minimized or hidden, and hides when the main window is restored. Toggling off instantly hides the buddy.

---

### **Autopilot - The Autonomous Chief of Staff**

- **Autopilot Dashboard.** Dedicated page (`/autopilot`) with four sections: Control Room, Execution Board, Identity & Memory, and Live History.
- **Deep-Dive Interview.** Multi-turn interview that learns your primary goal, niche, budget, and constraints. Saves your profile.
- **Master Plan Generation.** Skales generates a structured roadmap and task list from your profile. Tasks are pushed directly onto the Execution Board.
- **OODA Self-Correction Loop.** If a sub-task discovers new context (dead website, changed pricing, failed dependency), Autopilot autonomously rewrites, deletes, or reprioritises pending tasks and logs the reason with a full audit trail.
- **Human-in-the-Loop Approval Gates.** Tasks involving mass communications, file deletion, or financial transactions auto-flag for approval. The runner pauses until you click Approve or Reject on the Execution Board.
- **API Cost Control.** Configurable max calls per hour and pause-after-N-tasks counter. If a limit is hit, Autopilot pauses and waits for acknowledgment.
- **Anti-Loop Protocol.** Automatic retry tracking. After repeated failures a task is permanently blocked, never retried again.
- **Daily Stand-Up Report.** First-person morning briefing from completed, blocked, and in-progress tasks, plus recent log entries.
- **Execution Board (Kanban).** Full CRUD for tasks: Add, Edit, Cancel, Delete. Filter by state. Shows provider and model, re-plan badge, priority selector, and per-task approve/reject UI.
- **Live History Terminal.** Dark terminal-style log viewer. Colour-coded by level. Auto-polls and caps at 500 entries.

### **Meta-Agent - Universal Skill Dispatcher**

- **Headless Skill Execution.** Autopilot has isolated access to every active skill. Background executions never touch the foreground UI, chat history, or active sessions.
- **Internal Group Chat.** Spawns parallel calls with different personas to reach a consensus on complex decisions.
- **Skill routing syntax.** Tasks can explicitly route to a specific skill handler.

### **Voice Chat Interface**

- **Mic Button.** Amber-styled microphone button between New Session and History (visible only when Voice Chat skill is active).
- **Voice Chat Mode.** Dedicated fullscreen input overlay with status labels (Idle, Recording, Transcribing, Thinking, Speaking), animated pulse ring when recording.
- **Whisper Transcription.** Routes to Groq Whisper first, falls back to OpenAI Whisper.
- **TTS Playback.** ElevenLabs TTS with browser fallback.

### **Skill AI - Custom Skill Ecosystem**

- **ZIP Upload.** Upload a `.skill.zip` to install a new capability. Skales extracts, validates, and loads without restart.
- **AI Scaffolding.** Describe a skill in plain language. Skales generates the full skill definition, handler code, and metadata.
- **Skills Page.** Manage installed custom skills. Enable, disable, view metadata, delete. Isolated sandboxed execution.
- **Security Warning.** All uploaded skills display a security advisory banner before activation.

### **Documents Generation**

- **Word (.docx).** Generate formatted Word documents from natural language.
- **PDF.** Every document request simultaneously generates a PDF version.
- **Excel (.xlsx).** Create spreadsheets with data, formulas, and formatting.
- **Output.** Files saved locally and linked directly in the chat response.

### **Google Places**

- **Nearby Search.** Find restaurants, shops, services near any address or coordinates.
- **Place Details.** Fetch business hours, ratings, reviews, website, phone number.
- **Geocoding.** Convert addresses to coordinates and vice versa.
- **Directions.** Get turn-by-turn navigation data between two points.
- **Photo URLs.** Retrieve Google Places photo references.

### **Network Scanner**

- **LAN Discovery.** Scans the local subnet for devices.
- **Port Detection.** Reports open ports per device. Specifically detects other Skales instances.

### **Media Casting (DLNA/UPnP)**

- **Discovery.** Finds DLNA/UPnP media renderers on the LAN.
- **Cast Controls.** Play, Pause, Stop, Seek, and Set Volume on discovered devices.

### **v5 Polish - Stability, Identity & Infrastructure**

- **Proactive Check-In Cron Loop.** Cron jobs fire automatically on their schedule. In-memory deduplication prevents double-fires within an hour.
- **Voice Chat Mic Crash on HTTP.** Users on plain HTTP now receive a clear error message instead of a silent crash: "Microphone access requires a secure connection (HTTPS or localhost)."
- **Windows App User Model ID.** Toast notifications and taskbar entries now display "Skales" instead of the Electron app ID.
- **Capabilities Registry v5.0.0.** Added 7 new v5 skills: autopilot, custom skills, places, documents, voice chat, network scanner, casting.
- **Mobile UI Polish.** Removed "Talking to: " prefix from the agent selector. Header buttons collapse to icons on mobile.
- **Network & Devices Page** (`/network`). New page with two tabs: Network Scanner (mode selector, live device list with Skales-flag badge) and DLNA Casting (discover renderers, device picker, cast/pause/stop controls).
- **Skill AI Enhancements.** Added "requires API keys" toggle in the Custom Skills generation UI. Enabled required-secrets in the generated skill manifest. Conditional UI Playbook guidance injected when the skill has a UI.
- **Fluid Identity.** Rewrote how Skales builds its self-context. Includes current time, who Skales is, who the user is, key learnings, and recent memory highlights.
- **Agent-to-Agent Protocol** (`/api/agent-sync`). New endpoint supporting ping, handshake, delegate, and status operations. Optional bearer-token authentication.

### **Wake-Up Crash Fix**

- **Global error boundary** catches render errors and async errors. Shows a Skales-themed fallback with a "Reload Skales" button.
- **Polling Guards.** Background polls pause when the window is hidden to prevent wake-up crashes. Applied to voice, video poll, Telegram poll, inbox check, calendar reminders, memory scan, and email check.

---

## **4.0.0** - The Desktop Edition (February 2026)

### **New Features**

- **Native Desktop App.** Skales is now a proper desktop application for Windows and macOS. Install it once. No terminal, no manual server starts, no browser required. Launch it like any other app.
- **Single-Instance Lock.** Opening Skales a second time focuses the existing window instead of spawning a duplicate.
- **Smart Port Detection.** If port 3000 is occupied, Skales automatically tries 3001 and 3002 before failing gracefully.
- **Launch at Login.** Toggle in Settings > Desktop App to start Skales automatically when you log in. Works on both Windows and macOS.
- **Graceful Shutdown.** Skales now signals the internal server on quit and waits for in-flight tasks to finish before force-killing. No more torn bot sessions or half-written data.
- **Hidden CMD Window on Windows.** No more console window flash on startup.
- **macOS app bundle** includes proper copyright, version strings, privacy usage descriptions, and local networking permissions.
- **Home Directory Data Storage.** All user data now lives in the user's home directory, not inside the app bundle. Data persists across updates and reinstalls.
- **macOS Backup Fix.** ZIP import no longer crashes on macOS.

### **Bug Fixes**

- Fixed: ZIP Import overwrites existing data for full recovery
- Fixed: App performs full relaunch after backup import to clear cache

---

## **3.5.0** - The Connections Update (February 2026)

### **New Features**

- **X / Twitter Integration.** Connect your Twitter/X account via OAuth 1.0a. Skales can post tweets, read your timeline, fetch @mentions, and reply to tweets, from the chat interface or via Telegram. Three permission modes: Send Only, Read & Write, Full Autonomous. API keys stored securely locally.
- **Safety Mode.** Three-level command safety system (Safe, Advanced, Unrestricted). Safe mode blocks destructive shell commands (rm -rf, format, dd, fork bombs, etc.) outright. Advanced mode pauses dangerous commands and asks for Approve or Reject. Unrestricted mode disables all blocking for power users.
- **OpenRouter Telegram Vision Fix.** Image uploads via Telegram now work correctly when OpenRouter is the active provider. Skales auto-detects non-vision-capable models and falls back to a vision-capable one.
- **Secure Clipboard Fallback.** Clipboard copy works on HTTP and Tailscale connections (not just HTTPS).

### **Bug Fixes & Improvements**

- Fixed UUID generation breaking on HTTP connections (Tailscale, LAN IPs).
- Fixed mobile input bar disappearing behind the keyboard on iOS and Android.
- Fixed chat scroll-jump when new messages arrive while scrolled up.
- Fixed hydration mismatch on mobile chat page.
- Fixed Telegram memory wordcloud rendering edge case.
- Fixed Playwright cookie banner auto-dismiss not triggering on some pages.
- Improved proactive AI personality. Skales now occasionally initiates conversation based on context.
- Lio AI workspace fixes: invisible projects now visible, chat history preserved across sessions, failed build status resolved.
- Email: Trusted Address Book feature, HTML email rendering, IMAP namespace fix, timezone normalization for event timestamps.
- ElevenLabs TTS fallback chain improved. Now gracefully falls back to Google TTS on API errors.
- macOS: uninstall script renamed for consistency with all other launcher scripts.
- Setup scripts: improved admin rights handling, clearer UX messages, better error reporting.

---

## **3.0.0** - The Power Update (February 2026)

### **New Features**

- **Lio AI - Code Builder.** Multi-AI code builder using Architect, Reviewer, and Builder model pipeline. Build entire apps, websites, and scripts from plain-language descriptions. Navigate to the Code tab to use it.
- **Browser Control.** Headless Chromium automation. Navigate, click, type, scrape, and screenshot any website. Requires Vision Provider.
- **Vision Provider.** Configurable vision model for image analysis, desktop screenshots, and Browser Control. Supports Google, OpenAI, Anthropic, OpenRouter, Groq.
- **Auto-Update System.** One-click update download and installation with progress tracking, automatic backup, and rollback on failure.
- **Group Chat Multi-AI.** Lio AI uses multiple AI models simultaneously for architecture review.
- **Enhanced Memory.** Improved bi-temporal memory system.

### **Bug Fixes & Improvements**

- Fixed Telegram image analysis routing (no longer triggers duplicate reasoning loops)
- Browser Control correctly detects Playwright installation status
- Lio AI time estimates now show realistic values (2-7 min) instead of utopian numbers
- Vision provider label in chat correctly shows active provider name

---

## **2.0.0** - 2026-02-23

### **Added**

* **Message Queue.** Messages are queued when Skales is busy processing. Queued messages are shown in the chat UI with a counter badge. You can cancel individual queued messages or the currently-processing message. Works across Chat, Telegram, and WhatsApp interfaces.

* **Google Calendar Skill.** Read, create, edit, and delete Google Calendar events via OAuth. Skales can check your schedule, add events with reminders, and surface upcoming events as context in every conversation. Configurable in Settings > Skills > Google Calendar.

* **Gmail / Email Skill.** Full IMAP and SMTP email management. Fetch inbox, read threads, compose, reply, search, move, and delete emails. Clean text rendering of HTML emails. Approve and reject safety gates for send and delete operations. New email notifications appear as a banner on the dashboard. Configurable in Settings > Skills > Email.

* **Bi-Temporal Memory System.** Periodic memory scan extracts user preferences, facts, and action items from recent conversations. Memories carry both a valid-time (when the fact is true) and a transaction-time (when it was recorded). Relevant memories are injected as context before every response using local keyword extraction. No external embedding API required.

* **Telegram Admin Interface.** Remote-control Skales from Telegram using inline keyboard menus. Switch providers, models, and personas. Toggle skills on or off. View real-time status. Export conversation data. Admin-only access with PIN protection.

* **Killswitch.** Emergency hard-stop for all AI activity, triggerable via Dashboard button, Telegram `/killswitch` command, or automatically on RAM overload and detected infinite loops. Generates a detailed incident log on the desktop on activation.

* **Multi-Persona Group Chat Skill.** Multiple models with distinct personas discuss a user question in sequential rounds. Fully configurable: participants, language, number of rounds, and personas. Discussions can be exported as Markdown.

* **Autonomous Execute Mode.** Opt-in mode where Skales autonomously handles complex multi-step tasks. Presents a plan for approval, then executes step-by-step with progress updates and approve and reject checkpoints for critical actions (file writes, email sends, deletions). Available via chat and Telegram.

* **Website & Search Security Blacklists.** Domain blocklist prevents Skales from fetching dangerous or inappropriate websites. Word filter blocks harmful search queries before they reach the search API. Both are toggle-controlled in Settings > Security with curated default lists included. Fully customizable from the UI.

* **Responsive UI.** Full mobile and tablet support across the entire dashboard. Collapsible sidebar with overlay, mobile header, touch-optimized controls, and proper viewport handling.

### **Changed**

* Upgraded the internal skill registry to support new skill types (Calendar, Email, Group Chat, Execute Mode).
* Enhanced Telegram handler to support inline keyboard menus and all new admin commands.
* Memory system runs alongside the existing knowledge base (non-breaking additive enhancement).
* Email tools have appropriate approval gates: delete and reply require confirmation, move is automatic.
* Email bodies are converted from HTML to text for cleaner, token-efficient context.
* Dashboard email notification bar shows a single aggregated row (count and latest sender) instead of stacking multiple banners.

### **Fixed**

* Fixed message loss when sending messages while Skales was already processing a response (all messages are now safely queued).
* Fixed security blacklist toggle switches not animating.
* Fixed IMAP MOVE operation fallback on servers that don't support the MOVE extension.

---

## **0.9.0** - 2026-02-19

### **Added**

* **Weather Tool.** Integrated Open-Meteo API for free, keyless 7-day weather forecasts and geocoding.
* **Image Generation Skill.** Integrated Google Imagen 3 via the new Chat Skill Toolbar. Supports multiple aspect ratios and styles.
* **Video Generation Skill.** Integrated Google Veo 2 with asynchronous polling directly in the chat interface.
* **Skills Management Page.** New UI to toggle individual skills (Image Gen, Video Gen, Summarize, Weather) on or off.
* **Chat Skill Toolbar.** Added a "Sparkles" icon to the chat input to access generation panels.
* **Smooth Preloader.** Loading animations (Spin-Ring, Gecko, Bouncing Dots) for better UX.

### **Changed**

* **Persona System Overhaul.** Rewrote all 5 Personas (Default, Entrepreneur, Coder, Family, Student) with deep-dive prompts to give them distinct voices.
* **Agentic Loop Enhancements.** More iterations to handle complex multi-file tasks. Added a visual step-indicator and fixed the stuck-state UI bug.
* **File System Security Toggle.** Added a strict toggle in Settings (Workspace Only vs. Full Access) to sandbox file operations.
* **Self-Awareness (Capabilities Registry v1.4).** Skales can natively audit its own connections, verify identity files, and report on system health via tools.
* **Decoupled Notifications.** The internal scheduler is no longer hard-tied to Telegram, allowing for a universal system inbox.

### **Fixed**

* Fixed Groq TTS issues with a reliable HTTP fallback to Google TTS.
* Auto-switch logic for Vision models correctly triggers when images are pasted or uploaded.
* Fixed the "Enter to send" behavior when images are attached in the chat input.