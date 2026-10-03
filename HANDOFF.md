# PaperVault - Session Handoff

Repo: `harshgujjar/questionpapers` - work on branch `claude/cool-fermi-ih8atb`.
Status at handoff: **planning and mockups done. No application code written yet.** The repo is empty apart from this file.

## 1. Goal

A free, dynamic, Gen Z-friendly site plus an Android app (Java) that stores previous-year question papers for:

| Course | Year / Sem | Subject |
|---|---|---|
| BCA | Year 1, Sem 1 | Applied Mathematics-1 |
| BCA | Year 3, Sem 5 | Software Engineering |

The owner wants to earn income once students use it. The owner has **no prior experience** with React, Vercel, GitHub or Firebase, so explain steps plainly and give click-by-click guides for anything needing their own account.

## 2. Decisions already made

- **Stack (all free tiers):** React (Vite + Tailwind) on **Vercel**, code on **GitHub**, **Firebase** (Firestore for paper metadata, Auth for admin login; the owner says they are on the free plan).
- **PDFs live in Cloudinary.** Firestore stores only the Cloudinary link. The admin pastes the link into an admin page.
- **Mobile app:** native **Java** Android app showing the live website in a WebView, plus native extras (splash, offline error page, share, push notifications later). The app loads the live site, so updating papers never needs a new APK.
- **Direct APK link:** build the APK, commit it to the repo (public repo needed), and add a GitHub Actions workflow to rebuild it. Link form: `github.com/harshgujjar/questionpapers/raw/<branch>/downloads/QuestionPapers.apk`. I could not create GitHub Release pages from this environment (no tool for it).
- Site name **"PaperVault" is a placeholder.** Ask the owner for the real name.
- Do **not** create a PR unless asked.

## 3. Phases

1. **Phase 1:** React site: home, subject page, paper list from sample data, search, dark mode, Cloudinary links. Make it match the mockups.
2. **Phase 2:** Firebase: admin login, add/publish papers (paste Cloudinary URL), bookmarks, upvotes.
3. **Phase 3:** PWA install, SEO (one page per paper, e.g. "BCA Software Engineering 2023 paper"), WhatsApp share cards, PDF stamping with site name/QR.
4. **Phase 4:** Java Android app + `build.sh` + GitHub Actions to publish the APK.
5. **Phase 5:** Push notifications, quiz/streaks/leaderboard, exam countdown, AdSense, premium content (solved papers, important questions).

The owner has **not yet said "go"** to start Phase 1. Confirm, then begin.

## 4. Mockups

Published design (static, private to the owner): https://claude.ai/artifact/Ki5VyV1xfohXHaP3Gc7Adf

Five boards: Website Home, Website Subject page, Android Home, Android Papers list, Android PDF viewer. The owner has not given feedback on them yet.

Look to reproduce:
- Dark-first. Colours: background `#0E0F13`, surface `#181A21` / `#21242D`, border `#2A2D38`, text `#F3F4F6`, muted `#A3A8B8`.
- Accents: lime `#C8FF3D` (primary, Applied Maths-1) and violet `#8B7CFF` / `#B9AEFF` (Software Engineering).
- Fonts: Space Grotesk (headings), DM Sans (body). Rounded cards, big type, 44px+ touch targets, no emoji, inline stroke icons.
- Papers and numbers in the mockups are **sample data**.

## 5. Android build - verified working in this environment

The sandbox has Java 21 and no Android SDK, and `dl.google.com` is blocked. A no-Gradle build still works (same approach as the owner's other project, `DavanStudentWidget`):

1. `apt-get install -y aapt apksigner zipalign dalvik-exchange`
2. `android.jar` from Maven Central: `https://repo1.maven.org/maven2/org/robolectric/android-all/14-robolectric-10818077/android-all-14-robolectric-10818077.jar` (about 137 MB; it was rate-limited once, retry after a few seconds). Save as `~/tc/android.jar` or point `ANDROID_JAR` at it.
3. This jar has **no Java core classes**, so in `build.sh` use `javac -classpath "$ANDROID_JAR"` instead of `-bootclasspath`.
4. Steps: `aapt package` (R.java), `javac`, `dalvik-exchange --dex --min-sdk-version=24`, `aapt add classes.dex`, `zipalign -p 4`, `apksigner sign`.

I rebuilt the owner's `Davan.Student_w152.apk` with this chain as proof.

**Keystore:** generate a **new** keystore for this app (`keytool -genkeypair ...`). Keep it and its password **out of git**, give the owner the file to back up, and read the password from a GitHub secret or env var. Every update must use the same key. The Davan project's keystore and password are hardcoded in its `build.sh`; do not copy them here, and do not commit that zip.

## 6. Traffic strategy (the owner's worry: students share the PDF on WhatsApp and never return)

- Stamp PDFs with the site URL/QR; the Share button shares the **link** (with preview card), not the file.
- Give return reasons: exam-season updates, most-asked questions per unit, solved answers and notes, push alerts, quiz/streaks, exam countdown, doubt board, a Telegram/WhatsApp channel the owner controls.
- SEO page per paper, so each new student batch finds the site.
- Referral unlocks (e.g. invite 3 friends for solved papers).

## 7. Income plan (be honest: income follows traffic, expect months)

1. Traffic first (college groups, Instagram reels, Reddit/Quora, SEO).
2. Google AdSense once there is steady traffic and real content.
3. Affiliate links (books, courses, laptops).
4. Premium extras via UPI/Razorpay (solved papers, notes, important questions).
5. Sponsored spots for local coaching/tutors.
6. Expand to more subjects, semesters and colleges.

## 8. Caveats to keep in mind

- Free-tier limits change. Verify Firebase and Cloudinary free quotas in their consoles before relying on them.
- Confirm the university allows redistributing its past papers.
- Users sideloading the APK will see an Android "unknown source" warning. Play Store is a one-time $25 later; note Play may dislike thin WebView-only apps, so add real native features before submitting.
- Firestore security rules: public read, **admin-only write**.

## 9. Owner action items (I cannot do these for them)

- Create Firebase project (Spark/free), enable Firestore and Auth, copy the web config keys.
- Create a Vercel account and import the GitHub repo.
- Create a Cloudinary account and upload PDFs (or send links); decide on the final site name.
- Make the repo public if they want the direct APK link.

## 10. Next step for whoever picks this up

Ask the owner to confirm "go", the site name, and whether the mockup look is approved. Then scaffold Phase 1 in this repo on `claude/cool-fermi-ih8atb` with sample data, commit and push, and walk the owner through connecting Vercel.
