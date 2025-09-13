# ChatGPT Prompt Input

<img width="819" height="107" alt="CleanShot 2025-09-13 at 12 37 09@2x" src="https://github.com/user-attachments/assets/aeafc507-9f4c-49de-89d7-afe4dabbaaa1" />

```
::PromptInput
  .AddButton
    .click()                              => ::AddMenu

  @idle
    ::VoiceModeButton
      .start()                            => ::PromptInput@recording
    ::RecordButton
      .on()                               => ::PromptInput@recording

  @typing
    .SubmitButton
      .click()                            => [Chat]
    ::VoiceModeButton@hidden
    .AcceptButton@hidden

  @recording
    ::RecordButton
      .cancel()                           => ::PromptInput@idle
    .AddButton@disabled
    .SubmitButton@hidden
    .AcceptButton                         # VoiceModeButton → AcceptButton
      .accept()                           => ::PromptInput@typing
```
