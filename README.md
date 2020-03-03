# meetings

This repo contains the meeting archives for the W3C-CCG (Credentials Community Group), including transcripts based on the IRC logs and audio files for our regular meetings, ancillary material such as slide presentations, etc.

## Notes on archive process

This section descibes how the chairs archive W3C-CCG meetings, including sending the agenda, how to run the meetings, and how to publish CCG minutes transcript and audio after. This information is also use for leads and other facilitators archiving any alternative CCG meetings such as task forces and special meetings.

### Before Meeting (W3C-CCG Chairs)

* Update Announcement GitHub page https://w3c-ccg.github.io/announcements/
* Clear all old ["action: review next" issues](https://github.com/w3c-ccg/community/issues?q=is%3Aopen+is%3Aissue+label%3A%22action%3A+review+next%22) in CCG issues. 
* Review all [community issues](https://github.com/w3c-ccg/community/issues)  and choose 1-3 and tag "action: review next"
* Open the [W3C-CCG Planning & Agenda Email Draft](https://docs.google.com/document/d/1PbyZ0UtI6yzr5V_r-iseVyrZIWPm1BLXWsLeEV-mJvY/edit) in Google Docs 
* Updated all the items highlighted in yellow, in particular adding new agenda items for week starting with item 7.
* If there is a guest presentation, add it to the appropriate dated meeting [archives folder](https://github.com/w3c-ccg/meetings/)  before the meeting.
* Go to [CCG Email Send Agenda" Google Script](https://script.google.com/d/1QcV9INTap2Ke0gEkMTAovlZ-Cg3MIrYzxvU5h7SPKYvUrAuHnUCqgAHD/edit) - select function "sendAgenda" and click play. 
* Confirm in the [CCG mail archives](https://lists.w3.org/Archives/Public/public-credentials/) that the agenda was sent correctly

### Before Meeting (Task Force & Others)

* Send agenda to public-credentials@w3.org before each meeting
* If there is a guest presentation, add it to the appropriate dated meeting archives folder https://github.com/w3c-ccg/meetings/ before the meeting.

### During Meeting (All meetings)

* Make sure to link to the agenda at the beginning of the meeting ("Agenda: ...")
* Make sure the scribe is identified ("Scribe: ...")
* Make sure topics are labeled when the topic changes ("Topic: ...")

### Generate the minutes (for CCG meetings, task forces and other recorded meetings)

The chairs will need to give you access to the [script that launches it](https://script.google.com/d/16afjkO2wiKTHFBdM1-e4xqQ6YNX5LeqDpoSTB1Wrgqa8AlVD3GDjeqcf/edit).

What it does:
- Creates the html files and checks into github
- Emails summary to ccg email group
- Tweets with the w3c_ccg account

And you're done!
  
#### Not happy with the minutes? Clean them up

- Go to [the online scribe tool](https://w3c-ccg.github.io/meetings/scribe-tool/) and copy/paste irc.log into the text input box at the bottom. 
- Things to check
    - Ensure there's a link to the agenda ("Agenda: ...")
    - Ensure the topics are labeled ("Topic: ...")
    - Ensure the scribe is identified ("Scribe: ...")
    - Look for any find/replace suggestions in irc.log and update them ("s/../..")
    - Ensure aliases have matches (see people.json file)
- Overwrite the `irc.log` with the edited file and commit the changes via github
- The rest is handled for you; a github action will generate html and publish


#### Details about how it works
   
At the end of every call, the raw IRC log and audio recording are uploaded to:

* https://w3c-ccg.s3.digitalbazaar.com/minutes/YYYY-MM-DD-irc.log
* https://w3c-ccg.s3.digitalbazaar.com/minutes/YYYY-MM-DD-audio.wav

We use github actions to publish the minutes, and (optionally) to make updates

#### To generate minutes for date

The script runs the following command. Note the script adds the date value (e.g. 2020-01-07) and has the github API token with permissions to this repo.
```
curl -H "Accept: application/vnd.github.everest-preview+json" \
    -H "Authorization: token <token>" \
    --request POST \
    --data '{"event_type": "generate_minutes", "client_payload": { "date": "<date>"}}' \
    https://api.github.com/repos/w3c-ccg/meetings/dispatches
```

- Trigger: run the curl command (see above)
- Actions:
    - Download and generates minutes for <date>
    - Checks into github
    - Email the minutes to the CCG alias
    - Tweets link to minutes
- Github Action File: [generate_minutes.yml](https://github.com/w3c-ccg/meetings/blob/gh-pages/.github/workflows/generate_minutes.yml)

If you're not happy with the minutes, you can update them

#### To update the minutes for date

- Trigger: Kicks off when someone with access) checks in cleaned up irc.log file
- Does the following:
    - Updates html minutes and checks into github
- Github Action File: [update_minutes.yml](https://github.com/w3c-ccg/meetings/blob/gh-pages/.github/workflows/update_minutes.yml)

### Cleaning up the Audio

- Open the audio-raw.wav file in an editor (see setup above):
- Delete all audio before the Chair starts talking about the Agenda
- Delete all audio after the first person hangs up at the end of the call.
- Save the new file as audio.wav or encode to .ogg (file name audio.ogg) at 32kbps
    - For example: `oggenc -b 32 audio.wav`

### Comments/Bugs/Future Improvements
- We don't have audio integrated into this process yet. [See details below for what needs to be done]((#cleaning-up-the-minutes))
- Reducing number of steps:
    - This can be reduced to 1 step once we get into better scriibe hygiene habits. The separate cleanup won't be necessary
    - We could also try kicking this off on a schedule or using an s3 file watcher approach
- Use github caching action because this repo is large
- Make multiple tos not bcc
- Add wait before tweet
