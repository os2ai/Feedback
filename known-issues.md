# Known Issues

## 1. Disable default prompt suggestions

![Image showing prompt suggestion ui](https://github.com/user-attachments/assets/4d7f29f4-f711-4830-8c9c-d421183efdd2)

The prompt suggestions can not be disabled via infrastruture configuration in openwebui.
It can be overwritten by utilizing [DEFAULT_PROMPT_SUGGESTIONS](https://docs.openwebui.com/reference/env-configuration/#default_prompt_suggestions).
Status: koordinationsgruppen approved (2026-07-02) disabling them via a patch using environment variables; core team approved ASAP start 2026-08-19; issue closed 2026-09-11 after a patch PR (2026-08-23). Whether/when the disablement reached deployments is not recorded in the issue.

### 1. Mitigation

As admin, go to "Administrationspanel" -> "Instillinger" -> "Modeller" -> "Instillinger" -> "Prompt Suggestions" -> Delete the prompt suggestions -> "Gem"

### 1. References

- [os2ai/Feedback/issues/9](https://github.com/os2ai/Feedback/issues/9)
- [os2ai/Feedback/issues/4](https://github.com/os2ai/Feedback/issues/4) – changes to admin settings are reset by the infrastructure configuration on updates, which can bring the suggestions back
- [open-webui/discussions/15710](https://github.com/open-webui/open-webui/discussions/15710)
- [open-webui/issues/15386#issuecomment-3070458502](https://github.com/open-webui/open-webui/issues/15386#issuecomment-3070458502)

## 2. Answers are cut off mid-sentence

Answers from the OS2ai models (seen with Mistral and GPT-oss-120B) can stop mid-sentence, and the "Fortsæt" button frequently fails as well. The cause is the LiteLLM "message trimming" guardrail, which does not take the special message order required when using tools into account, combined with context windows that are too small (e.g. a 8192 token limit copied from the Mistral setup when switching to a larger model).

### 2. Mitigation

Configure the model's context window to match the actual LLM, and disable the message-trimming guardrail for the model if needed. Note that an empty guardrails list does NOT disable the guardrail because it has `default_on: true`; a working example is [demo-gitops commit f09c268b](https://github.com/os2ai/demo-gitops/commit/f09c268b581b94822667a3e3c768cfb0463054a8), applied for gpt-oss-120b at Kolding/Holstebro (reporters have not confirmed the fix). A guardrail change adding tool-call support was proposed in [os2ai/helm-deployments PR #28](https://github.com/os2ai/helm-deployments/pull/28) (2026-05-08, not verifiable from the issue whether it merged or resolved the original Mistral problem).

### 2. References

- [os2ai/Feedback/issues/1](https://github.com/os2ai/Feedback/issues/1)
- [os2ai/helm-deployments PR #28](https://github.com/os2ai/helm-deployments/pull/28)
- [os2ai/demo-gitops commit f09c268b](https://github.com/os2ai/demo-gitops/commit/f09c268b581b94822667a3e3c768cfb0463054a8)

## 3. "Index out of range" errors when uploading files

Uploading files can fail with an "Index out of range" / 400 Bad Request error. Open WebUI measures chunk sizes with the OpenAI tokenizer (cl100k_base), while the embedding model (multilingual-e5-large-instruct) uses the xlm-roberta tokenizer, so chunks sized for 400-440 tokens can exceed the embedding model's 512 token limit.

### 3. Mitigation

Stopgap, called "den naive løsning" by its author: lower `CHUNK_SIZE` (e.g. from 440 to 350, giving a 64 token buffer). Decided fix (core-team decision 2026-06-24): a patch to Open WebUI that chunks using the tokenizer of the configured embedding model (cableman, targeted at RC 0.6); merge status not recorded in the issue. Replacing the ingestion pipeline altogether is tracked in [os2ai/Feedback/issues/38](https://github.com/os2ai/Feedback/issues/38). Note: the admin-settings documentation lists the standard "Chunk størrelse" as 500, which conflicts with both the 440 used in demo-gitops and this bug's error behavior; the page itself flags that its list may not match the actual standard settings, so treat its chunk values as unverified.

### 3. References

- [os2ai/Feedback/issues/8](https://github.com/os2ai/Feedback/issues/8)
- [os2ai/demo-gitops values.yaml (CHUNK_SIZE)](https://github.com/os2ai/demo-gitops/blame/4db45a644a687e9ce4f1a1addb35be81beacd5ac/applications/openwebui/values.yaml#L403)
- [os2ai/Feedback/issues/38](https://github.com/os2ai/Feedback/issues/38)

## 4. Admin settings revert in the managed solution

In installations where Open WebUI is configured as code, settings changed in the admin panel flip back and forth (roughly every 10 seconds / on refresh) between the administrator's values and the infrastructure configuration: Open WebUI saves changes to its database but is configured to ignore them, so the two race each other.

### 4. Mitigation

Guidance is published: [Indstillinger i administrationspanelet](https://os2ai.github.io/documentation/admin/admininfo.html) (delivered via [os2ai/documentation/issues/4](https://github.com/os2ai/documentation/issues/4), closed completed 2026-10-14 through [os2ai/documentation PR #15](https://github.com/os2ai/documentation/pull/15), merged same day). It documents that admin-panel changes are temporary: saved to the database but overwritten and rolled back at the next release (potentially earlier) under Configuration as Code, including the multi-instance save quirk that produces the flip-flop symptom; it lists example critical settings (authentication, API, integrations, documents, code execution) with screenshots, explains the rationale, and routes change requests through the drifts-/applikationsleverandør (Computerome support listed; local IT-administrator where no vendor is used). The setting list is explicitly non-exhaustive ("Listen er ikke udtømmende") and may not match actual defaults, pointing to [helm-deployments values.yaml](https://github.com/os2ai/helm-deployments/blob/develop/applications/openwebui/values.yaml#L258-L615) as the source. The reset-on-upgrade complaint of [os2ai/Feedback/issues/40](https://github.com/os2ai/Feedback/issues/40) appears addressed by PR #15 (merged 2026-10-14, one day after #40's last update), so closure confirmation is owed on #40 and Feedback#4; note the published vendor-contact list has an unfinished "???" second bullet. Disabling the admin panel sections that are configured as code is tracked in [os2ai/Feedback/issues/10](https://github.com/os2ai/Feedback/issues/10), which waits on #4.

### 4. References

- [os2ai/Feedback/issues/4](https://github.com/os2ai/Feedback/issues/4)
- [os2ai/Feedback/issues/10](https://github.com/os2ai/Feedback/issues/10)
- [Indstillinger i administrationspanelet](https://os2ai.github.io/documentation/admin/admininfo.html) - delivered via [os2ai/documentation/issues/4](https://github.com/os2ai/documentation/issues/4), closed completed 2026-10-14 via [os2ai/documentation PR #15](https://github.com/os2ai/documentation/pull/15)
- [Vigtigt - Håndtering af administratorrettigheder i OpenWebUI](https://os2ai.github.io/documentation/admin/vigtig.html)
