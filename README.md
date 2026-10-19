# Suno

## Android interview practice: 10 Jetpack Compose exercises

These exercises are for you as a candidate preparing for Suno's 60-minute Android live coding interview. Each is a standalone task phrased as a prompt you might receive. They are invented practice questions, not actual or predicted Suno interview questions.

Start each session from the original starter project, rather than building on a previous solution. No solutions, implementation walkthroughs, or required architecture are included here.

### How to practice

- Before starting the timer, import and run the starter in Android Studio. Prepare any local fixtures or playable audio required by your chosen exercise.
- Allow 5 minutes to read and clarify the task, 45 minutes to implement, and 10 minutes to demonstrate behavior and discuss decisions.
- Prioritize the required behavior. Simple Material components and artwork placeholders are sufficient; matching Suno's visual design is not required.
- Choose your own state management and architecture. External documentation and AI are allowed, as described in your interview email.
- Unless a prompt says otherwise, state only needs to survive ordinary recomposition. Process restoration, disk persistence, authentication, and production backend changes are excluded.
- Acceptance checklists describe observable behavior. Use them to demonstrate your work; writing an automated suite is not required within the hour.

### How these exercises map to the role

The job description you supplied targets Senior and Staff Android engineers working across creation, discovery, playback, and social experiences. It emphasizes feature ownership, interactive media, polished animation, Android behavior, performance, and technical communication.

These exercises practice small slices of those responsibilities. Exercise 8 specifically requires animation; exercise 4 uses actual Media3 playback; exercise 7 models an asynchronous creation experience; exercises 5 and 10 cover social interactions. The remaining exercises practice discovery, library management, and media state. None requires implementing real AI inference.

Keep production architecture, profiling, instrumentation, and large-scale infrastructure in the discussion portion. You do not need to build a production platform within an hour. Use the questions after each exercise to explain how you would evolve your implementation without adding that work to its acceptance checklist.

### Starter project context

The starter currently displays a greeting. It includes a Retrofit client and `ApiService.getSongs(page, pageSize)`, plus nullable song models. Song information is nested under `Song.clip`, with fields including title, creator display name, ID, artwork URLs, audio URL, liked status, and like count. The response includes page and total-page metadata.

Exercises 1 and 3 use the supplied songs API. If it is unavailable, use equivalent local response fixtures with the same pagination and failure behavior. Other exercises use local fixtures and simulated operations; no additional Suno endpoints are assumed.

For API exercises, missing titles and creators should have readable fallbacks; missing artwork should show a placeholder. Ignore null song entries, null clips, and clips without a usable ID. The UI should remain usable with long text. Label icon-only controls so their purpose is accessible.

### Exercises

1. [Discover songs](#1-discover-songs)
2. [Search your library](#2-search-your-library)
3. [Browse a paginated catalog](#3-browse-a-paginated-catalog)
4. [Play a song](#4-play-a-song)
5. [Manage liked songs](#5-manage-liked-songs)
6. [Create a playlist](#6-create-a-playlist)
7. [Create a song](#7-create-a-song)
8. [Expand the now-playing experience](#8-expand-the-now-playing-experience)
9. [Manage a play queue](#9-manage-a-play-queue)
10. [Publish a song](#10-publish-a-song)

## 1. Discover songs

**Your task:** Build a discovery screen that lets someone browse songs returned by the starter's API.

**Inputs:** Request page 1 with a page size of 10. Display each song's title, creator, and artwork or a placeholder. Prepare empty and failed responses so those states can be demonstrated.

**Requirements:** Show initial loading, successful content, an empty state, and an error with Retry. Retry should attempt the same request again. Ordinary UI interactions and recomposition must not cause repeated fetches.

**Acceptance checklist:**

- [ ] Launching the screen loads and displays a scrollable song list.
- [ ] Loading is visible before the response arrives.
- [ ] Empty and error responses produce distinct, useful states.
- [ ] A failed request can be retried successfully.
- [ ] Missing fields and long titles do not break the screen.

**Outside this exercise:** Pagination, search, playback, and song mutations.

**Discussion only:**

- How would you separate reusable song presentation from discovery-specific loading and retry behavior?
- How would you measure scrolling performance and artwork memory use before choosing an optimization?

## 2. Search your library

**Your task:** Build a library screen where someone can find songs using a search field and a liked filter.

**Inputs:** Prepare eight local songs with unique IDs, titles, creator names, and liked flags. Include two songs with the same title by different creators, mixed letter casing, and at least three liked songs.

**Requirements:** Match the query against title or creator using case-insensitive substring matching. Ignore leading and trailing query whitespace. Provide All and Liked filters; the selected filter and search query apply together. A blank query shows every song allowed by the filter. Provide a clear-query action and a no-results state.

**Acceptance checklist:**

- [ ] Searching by title or creator works without case sensitivity.
- [ ] Changing filters preserves the current query.
- [ ] Clearing the query preserves the current filter.
- [ ] No matches shows an empty result message, and changing the query restores results.
- [ ] Songs with identical titles remain separate entries.

**Outside this exercise:** Networking, editing likes, sorting, and playback.

**Discussion only:**

- Which search and filter behaviors would you confirm with product before changing this feature?
- How would you keep typing responsive with a much larger library, and what evidence would justify server-side search?

## 3. Browse a paginated catalog

**Your task:** Build a song catalog that loads additional results when someone taps Load More.

**Inputs:** Use the starter's API with a page size of 10. For repeatable checks, prepare three equivalent response pages with valid page metadata, an overlapping song ID between two pages, and a controllable page-two failure. A separate response can represent an empty catalog.

**Requirements:** Load the first page on entry. Append subsequent pages, keeping the first occurrence of each song ID. Allow only one request at a time. Use page metadata to stop after the last page; an empty returned page also ends loading. Distinguish initial failure from failure to load more: an additional-page failure must retain existing songs and retry the failed page.

**Acceptance checklist:**

- [ ] Initial loading, empty results, and initial failure are handled.
- [ ] Load More appends songs without duplicates or replacing earlier results.
- [ ] Repeated taps during loading do not skip pages or issue concurrent requests.
- [ ] A failed additional page leaves existing content visible and can be retried.
- [ ] Load More disappears or is disabled when there are no more pages.

**Outside this exercise:** Automatic infinite scrolling, pull-to-refresh, search, and playback.

**Discussion only:**

- Which pagination guarantees would you agree on with backend engineers if songs could be inserted while browsing?
- How would you diagnose duplicate requests and measure network efficiency without hiding legitimate retries?

## 4. Play a song

**Your task:** Build a song list with a mini-player that remains visible while the list scrolls.

**Inputs:** Before the timed session, prepare three local song records containing IDs, titles, creators, and verified playable audio URLs. You may obtain these from the starter's song responses. Also prepare one deliberately invalid audio URL. Media3 ExoPlayer is already declared as a dependency.

**Requirements:** Tapping a song starts actual foreground audio playback and updates the mini-player. Rapid selections must leave the last selected song active; an earlier track's delayed loading or error must not overwrite the current state. Support play/pause and switching songs. Only one song may play at a time. Show buffering and playback failure; after failure, allow retry or selection of another song. When a track ends, show it as stopped and allow replay. Playback must stop and resources must be released when this screen is left.

**Acceptance checklist:**

- [ ] Selecting a song updates the mini-player and plays its audio.
- [ ] Play/pause controls both audible playback and the displayed state.
- [ ] Switching tracks stops the previous audio; after rapid selections, playback and the mini-player agree on the last selected song.
- [ ] An invalid URL produces a visible failure, and a valid selection recovers.
- [ ] Scrolling and recomposition do not restart the track or create overlapping audio; leaving the screen stops playback and releases the player.

**Outside this exercise:** Background playback, notifications, audio focus handling, seeking, queues, and automatic next-track playback.

**Discussion only:**

- Who should own the player as the app grows to multiple screens, and what would change for production background playback?
- How would you distinguish network buffering from player startup latency and measure time to audible playback?

## 5. Manage liked songs

**Your task:** Build All Songs and Liked views with immediate feedback when a like is changed, followed by a simulated save that can succeed or fail.

**Inputs:** Prepare six local songs with unique IDs, initial liked flags, and like counts. Include at least two liked songs. Counts already include the current user's like when the initial flag is true. Each simulated update takes about one second; choose its success or failure outcome before triggering it.

**Requirements:** Toggling a like immediately updates its icon, count, and membership in the Liked view. Liking adds one; unliking subtracts one. While that song's update is pending, prevent another change to it in either view; other songs remain interactive. Success keeps the change. Failure restores that song's previous flag, count, and membership, and shows feedback with the ability to try again. Switching views must preserve pending and committed state. Show an empty state when no songs are liked.

**Acceptance checklist:**

- [ ] Both views immediately reflect the same like state and count, including while saving.
- [ ] A pending update cannot be repeated for the same song, but another song can be changed.
- [ ] Success keeps the change; failure restores the prior state and permits another attempt.
- [ ] Unliking the final liked song shows an empty state; a failed save restores it to Liked.
- [ ] Repeated successful toggles do not drift counts; failure for one song does not undo another song's update.

**Outside this exercise:** Real network writes, offline synchronization, persistence, and playback.

**Discussion only:**

- How would you evolve this interaction if the server returned an authoritative like count or conflicting updates?
- Which signals would reveal frequent rollback or inconsistent UI state, and how would you distinguish update attempts from confirmed likes?

## 6. Create a playlist

**Your task:** Build an initially empty playlist with an Add Songs picker.

**Inputs:** Prepare eight local songs with unique IDs and one named, empty playlist. Include two songs with the same title to ensure selection uses identity rather than title.

**Requirements:** Open a picker, select multiple songs, and confirm or cancel. Confirm appends new songs in their catalog order. Existing playlist entries must not be added again. Cancel discards pending selections, and reopening starts a fresh selection. Support removing entries and display the playlist's song count. Preserve existing order when adding or removing songs.

**Acceptance checklist:**

- [ ] The initial playlist has a useful empty state and Add Songs action.
- [ ] Confirm adds the selected songs; cancel adds nothing.
- [ ] Adding again cannot duplicate existing entries.
- [ ] Removing a song updates the list and count immediately.
- [ ] Removing every entry restores the empty state.

**Outside this exercise:** Multiple playlists, drag reordering, persistence, networking, and playback.

**Discussion only:**

- Which picker behavior would you validate with product/design, and which UI boundaries could be reused without overgeneralizing?
- How would you measure picker opening and selection responsiveness with thousands of songs?

## 7. Create a song

**Your task:** Build a simplified creation form and show a simulated song generation progressing through Queued, Generating, and Ready.

**Inputs:** The form contains a description and an Instrumental switch. Prepare two local result songs. A simulated submission spends about one second Queued and two seconds Generating before reaching Ready or failing. Select the success or failure outcome before submission; failure occurs after Generating. No real generation service is required.

**Requirements:** Accept a nonblank description of at most 300 characters and show its character count. Create is unavailable for invalid input or while Queued or Generating. Keep the submitted description and Instrumental setting visible and fixed during the request. Show each status clearly, without inventing percentage progress. Ready displays two result songs. Failure shows feedback and permits retry from Queued without losing form values. Start each new attempt with no previous result songs visible.

**Acceptance checklist:**

- [ ] Blank, whitespace-only, and over-limit descriptions cannot be submitted.
- [ ] The Instrumental selection is reflected in the submitted request details.
- [ ] Queued and Generating are visible in order, with duplicate submission prevented.
- [ ] Ready displays exactly two results; failure does not display successful results.
- [ ] Failure preserves inputs, and retry passes through Queued and Generating before succeeding.

**Outside this exercise:** Real AI inference, server polling or streaming, percentage progress, credits, accounts, playback, and saving results.

**Discussion only:**

- What status and retry contract would you need from backend engineers to replace the simulation, including handling duplicate submissions?
- Which events would measure time spent queued, generation duration, and terminal failures without treating progress messages as completed songs?

## 8. Expand the now-playing experience

**Your task:** Build a compact player that expands into a full now-playing presentation through an animated transition.

**Inputs:** Prepare one local song with a title, creator, and artwork placeholder. Playback is simulated with a playing/paused flag; there is no audio, elapsed-time clock, or dependency on exercise 4. Start with the compact player paused.

**Requirements:** Show title, creator, artwork, and a play/pause control in both presentations. Provide a separate expand action on the compact player and a collapse action on the expanded player. Expansion and collapse must visibly animate rather than switch instantly. Android Back collapses the expanded presentation; when compact, Back follows normal screen behavior. Play/pause changes stay synchronized across presentations, and those controls do not accidentally expand or collapse the player. Repeated presentation changes during a transition must settle into a single consistent presentation with usable controls. The animation technique and appearance are yours to choose.

**Acceptance checklist:**

- [ ] The compact player starts paused and expands through a visible animation.
- [ ] Both presentations show the same song and current playing/paused state.
- [ ] Play/pause works in either presentation without changing which presentation is open.
- [ ] Collapse and Android Back return to the compact player through a visible animation without resetting playback state.
- [ ] Rapid expand/collapse actions settle without duplicate players, blocked controls, or inconsistent state.

**Outside this exercise:** Real audio, gestures, shared-element choreography, seeking, navigation between songs, persistence, and custom artwork loading.

**Discussion only:**

- How would you collaborate with design to preserve the intended transition while respecting Android Back behavior and accessibility?
- How would you measure dropped frames and identify unnecessary layout, rendering, or memory work during expansion?

## 9. Manage a play queue

**Your task:** Build an ordered queue with a current-song indicator and simulated playback controls.

**Inputs:** Prepare five local songs with unique IDs. The first song is initially selected. There is no actual audio or advancing playback clock.

**Requirements:** Tapping a row selects it. Next selects the following song and is disabled at the end. Removing a non-current song preserves the current selection. Removing the current song selects its successor, or its predecessor if it was last. Removing the final song clears the selection and shows an empty state. Display the current song's position within the remaining queue.

**Acceptance checklist:**

- [ ] Exactly one song is marked current whenever the queue is nonempty.
- [ ] Selecting a row and tapping Next update the indicator and position.
- [ ] Removing an earlier row does not accidentally change the selected song.
- [ ] Removing the current first, middle, and last song follows the specified rules.
- [ ] Empty queues expose no active selection or Next action.

**Outside this exercise:** Actual audio, drag reordering, repeat, shuffle, and persistence.

**Discussion only:**

- Who owns queue identity and selection, and how could the same behavior support real playback without rewriting the queue UI?
- How would you detect a mismatch between selected and playing tracks and verify responsiveness for a large queue?

## 10. Publish a song

**Your task:** Build a song detail screen with editable publishing settings.

**Inputs:** Prepare one local song initially set to Link-only with Allow remixing off. A simulated save takes about one second and can be deliberately set to succeed or fail before confirmation.

**Requirements:** Open a settings editor containing Link-only/Public visibility and an Allow remixing switch. These are independent settings for this simplified exercise. Confirm saves the draft asynchronously and prevents repeated confirmation while saving. Success updates the detail screen and closes the editor. Failure keeps the editor and draft available for retry. Cancel, Back, or dismiss discards edits when no save is in flight; disable dismissal during saving.

**Acceptance checklist:**

- [ ] Opening the editor reflects the currently committed settings.
- [ ] Draft changes do not affect committed details before a successful save.
- [ ] Cancel discards both pending settings.
- [ ] Failure preserves the draft and permits a successful retry.
- [ ] Saving cannot be triggered twice concurrently, and success updates both settings.

**Outside this exercise:** Real publishing, permissions enforcement, sharing links, authentication, and persistence.

**Discussion only:**

- Which backend guarantees would you need for publishing permissions, retries, and consistent settings across devices?
- Which events would distinguish a publish attempt, confirmed success, failure, and abandonment without recording private song content?

## After each session

Use your final 10 minutes to demonstrate the checklist and answer:

- Which behavior is complete, and what would you prioritize with another 20 minutes?
- How does your state stay consistent as users interact and Compose recomposes?
- Which edge case or failure was most important, and how did you verify it?
- What tradeoff did you make to finish within the hour?
- If you used AI, which suggestions did you accept, change, or reject? Explain one concrete limitation or mistake in its output and how you verified the final behavior.

Record your elapsed time and unfinished requirements so you can compare sessions without expanding the next exercise's scope. The discussion questions are prompts for explanation, not extra implementation requirements or claims about Suno's interview rubric.

### Senior and Staff self-review

- **Senior:** Can you explain how you would own this feature from clarifying the problem through shipping, monitoring, and iterating? Identify one quality risk and how you would test it.
- **Staff:** Which boundary could become a shared foundation for other features? Explain how you would validate the need, introduce it incrementally, help teammates adopt it, and evaluate its costs as usage and the team grow.

Discuss these at the level appropriate to your application. A small, correct implementation with clear tradeoffs remains the timed deliverable; building a platform, profiling suite, or monitoring pipeline is not required.

## Questions to bring to Suno

Choose a few that address what you still want to learn:

- How is ownership divided across creation, discovery, playback, and social features, and what would this role own first?
- How do Android engineers collaborate with product and design when shaping interactions and scoping a new feature?
- Which parts of the media stack are shared foundations today, and where do you most need new abstractions?
- Which performance and reliability measures guide Android releases, and how does the team investigate regressions?
- How do the expectations for Senior and Staff engineers differ in feature delivery, technical direction, and mentorship?

## Context and feature references

The interview format and permission to use AI come from your invitation email. Role alignment uses the full job description you supplied for the [Senior/Staff Android opening](https://jobs.ashbyhq.com/suno/f5826898-f3d2-4d11-aa03-17ecac31f28f?departmentId=53c2a563-73a5-4139-96ca-d07941990999&jr_id=6a56edec53b3962b910b3261). The supplied description emphasizes end-to-end ownership, creation/discovery/playback/social experiences, animation, performance, and technical leadership; it does not specify the live coding task.

Feature inspiration comes from Suno's documentation for [Android Simple creation](https://help.suno.com/en/articles/3726657), [Android Custom creation](https://help.suno.com/en/articles/3726721), [playlist-based inspiration](https://help.suno.com/en/articles/6882753), and [publishing songs to a profile](https://help.suno.com/en/articles/2551361). Exercise rules deliberately simplify the product and do not specify Suno's production behavior.
