# Patch for Twilio Flex Dual Channel Recording Plugin
This is a patch for the [Flex Dual Channel Recordings Plugin](https://github.com/twilio-professional-services/flex-dual-channel-recording/).  
It addresses the following:

1. Adds worker details for each reservation in the task attributes. This results in task completed event having the record of all the agents

Snippet:
```
const mediaObj = {
      url: recordingUrl,
      type: 'VoiceRecording',
      start_time: recordingStartTime,
      channels: ['customer', 'others'],
    };

    const workerObj = {
      attributes:workerState.attributes,
      WorkerSid:workerState.source.sid
    }

    switch (REACT_APP_RECORD_CHANNEL) {
      case 'worker':
        const newReservationAttributes = {};
        newReservationAttributes[reservationSid] = {
          media: [mediaObj],
          worker:workerObj
        };
        newAttributes = {
          ...attributes,
          reservation_attributes: {
            ...reservationAttributes,
            ...newReservationAttributes,
          },
        };
        break;
```


2. Fixes a bug in recording additional worker's conversation leg in case of transfers
  
```
  /* Original Code which could get reference to workers who already left the coversation
  const participantLeg = participants.find(
    (p) => p.participantType === REACT_APP_RECORD_CHANNEL 
  );
  */

  // Modified Code to pick only the current current worker
  const participantLeg = participants.find(
    (p) => p.participantType === 'worker' && p._source.status==='joined' && task.workerSid===p.workerSid
  );
```