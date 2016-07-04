# Lesson template for Neurohackweek

How to use this template:

1. Go to the <a href="http://import.github.com/new?import_url=https://github.com/neurohackweek/lesson-template
" target="_blank"> Github Importer</a>. In the top text box paste the url of this repo. In the bottom part
choose either "Neurohackweek" (if that's an option) or your own user account and
then enter the name of the lesson/repository that you wish to create.

2. Change the following variables in the `_config.yml` file:
   - `title`
   - `repo`
   - `root`
   - `email` (you can leave Ariel's address here, if you want).
   - `start_time` : this is the start time in minutes since midnight. For
     example, 9 AM is 540 (60 * 9).

3. Edit the content in the `_episodes` folder, adding images (into
  `assets/img`), code (into `code`), data (into `data`) as needed. Pay
  particular attention to the following:

  - Sections should be named `01-first-part.md`, `02-second-part.md`, etc to be ordered in the schedule.
  - Edit the headers of each of your sections. Editing the duration of both `teaching` and `exercises`
  - Add coffee breaks into the lesson. This keeps the timing of each section
    accurate.
