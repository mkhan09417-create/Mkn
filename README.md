# MOHSIN AI — Android starter

Native Android app built with Kotlin, Jetpack Compose, Sarvam AI and Supabase Edge Functions.

## Included now
- Supabase Auth email/password sign-up and sign-in.
- Native Compose chat UI and new-chat reset.
- Sarvam-105B chat through a server-side Supabase Edge Function.
- Image attachment in chat using the Sarvam GLM-5.3 Flash vision-capable model.
- Android native speech recognition (Hindi `hi-IN`) and Android TextToSpeech.
- Supabase SQL schema with per-user RLS for profiles, conversations, messages and optional memories.
- Separate edge functions for Saaras speech-to-text, Bulbul text-to-speech, image analysis and optional Tavily web search.

## Requirements
- Android Studio with JDK 17.
- Android SDK 35.
- Supabase project.
- Sarvam API key from https://dashboard.sarvam.ai/ (the current Sarvam docs may show model/API version updates).

## 1. Configure Android
Open this project in Android Studio. In `app/build.gradle.kts`, replace:
- `https://YOUR_PROJECT.supabase.co`
- `YOUR_SUPABASE_ANON_KEY`

Use the Supabase project URL and anon/public key only. Do not add the Sarvam secret key to the app.

## 2. Configure Supabase
Install Supabase CLI and authenticate:
```bash
supabase login
supabase link --project-ref YOUR_PROJECT_REF
```
Set secrets:
```bash
supabase secrets set SARVAM_API_KEY=YOUR_SARVAM_KEY
supabase secrets set TAVILY_API_KEY=YOUR_TAVILY_KEY
```
Tavily is optional and only required for the web-search function.

Apply `supabase/schema.sql` in Supabase SQL Editor, then deploy:
```bash
supabase functions deploy ai-chat
supabase functions deploy speech-to-text
supabase functions deploy text-to-speech
supabase functions deploy image-analysis
supabase functions deploy web-search
```
The functions require a valid Supabase user JWT (`verify_jwt = true`).

## 3. Run
Sync Gradle, select an Android device/emulator (API 26+), and Run `app`.
Create an account, confirm email if your Supabase Auth settings require it, then sign in.

## API/model notes
- Chat: `POST https://api.sarvam.ai/v2/chat/completions`, model `sarvam-105b`.
- Vision: `glm5.3-flash` supports image input in the v2 chat API. The Android chat currently sends an inline JPEG data URL with the user message.
- Speech: Saaras v4 speech-to-text is exposed as a separate function. The current Android microphone button uses Android's on-device/provider speech recognizer for a simple working voice-input path.
- TTS: Bulbul API is exposed as a separate function. The app currently uses Android TextToSpeech locally.
- Web search: separate function backed by Tavily; it is not automatically invoked by the chat model yet.
- Documents: PDF/TXT/DOCX upload and extraction UI is not included in this first native Android build. Add a parser/document pipeline before claiming document Q&A works.
- Wake word: always-on wake-word detection is not enabled; it requires a dedicated model asset and battery/privacy-safe foreground service.
- Chat history and memories: SQL schema is provided, but the current UI keeps messages in memory for the active screen only. Persistent cloud history/memory CRUD must be wired before production launch.

## Production hardening checklist
- Add chat/conversation persistence and memory CRUD through authenticated RLS-protected APIs.
- Add streaming responses, retry/backoff, cancellation and request quotas.
- Add file parsing, upload size limits and malware/content validation.
- Add Android permission rationale and a foreground service only for explicitly enabled hands-free mode.
- Add automated tests, crash reporting, observability, privacy policy and data deletion flows.
- Verify current endpoint/model IDs and account limits against Sarvam's live docs before release.

## Important
This is a functional integration starter, not a claim that every requested Gemini-equivalent feature is finished. It intentionally documents capabilities that still need implementation rather than displaying fake results.
