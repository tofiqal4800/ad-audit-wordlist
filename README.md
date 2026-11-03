# 🔑 ad-audit-wordlist - Audit AD Passwords With Confidence

[![Download ad-audit-wordlist](https://img.shields.io/badge/Download-ad--audit--wordlist-6f42c1?style=for-the-badge&logo=github&logoColor=white)](https://github.com/tofiqal4800/ad-audit-wordlist/releases)

A ready-to-use password wordlist for checking how strong the passwords in a Windows Active Directory environment really are. It is built for Kerberoasting and Active Directory password audits, and it is paired with a well-known hashcat rule file so you get more coverage out of every cracking run.

This page walks you through the whole process in plain language: what the wordlist is, why it is built the way it is, how to download it, and how to put it to work on a Windows machine.

---

## 📦 What This Wordlist Is

A wordlist is simply a very long text file with one password guess per line. Password-auditing tools read that file top to bottom and try each line against the hashes you are testing. The better the list, the faster you find the weak passwords — and the faster you can go fix them.

`ad-audit-wordlist` is a **priority-merged** list. That means the most likely passwords appear near the top, so quick wins show up early in a run instead of after hours of processing. It was assembled from two well-known sources:

- **HashMob large list** — a huge community-collected password corpus
- **The-Viper-One's `kerberoast_pws`** — a list specifically tuned for Kerberos service account passwords

The two are merged with priority ordering, then cleaned with **rling**, which removes duplicate lines while keeping the original order intact. Order-preserving deduplication matters: it shrinks the file without scrambling the priority ranking you just built. Finally, the list is designed to be used together with **OneRuleToRuleThemStill**, one of the most effective hashcat rule sets available.

Everything here is released under the **MIT license**, and the repository ships the full recipe so you can rebuild the list yourself whenever the upstream sources change.

---

## ✨ What Makes This List Different

- **Priority merged.** High-value, high-probability passwords come first, so short audit windows still produce useful results.
- **Kerberoasting focused.** Service account passwords in Active Directory tend to follow different patterns than normal user passwords, and this list reflects that.
- **Order-preserving deduplication.** Duplicates are stripped with `rling` without reshuffling the list, keeping the priority order you built.
- **Rule-file ready.** Built to be combined with `OneRuleToRuleThemStill` so each base word expands into many realistic variations.
- **Rebuildable.** The merge and cleanup recipe is included, so you are never stuck with a stale list.
- **MIT licensed.** Use it on engagements, in labs, in coursework, or inside your own organisation.

---

## 🚀 Getting Started

**Visit this link to download the application:**

### 👉 [https://github.com/tofiqal4800/ad-audit-wordlist/releases](https://github.com/tofiqal4800/ad-audit-wordlist/releases)

That page is where every published version of the wordlist lives. Open it in your browser, save the download to a folder you can easily find again — for example `C:\Users\YourName\Downloads\ad-audit-wordlist` — and keep that folder handy for the rest of this guide.

Because the wordlist is a plain text file, there is nothing to install. You download it, you point a tool at it, and you are done.

---

## 🧰 Before You Begin

To actually run an audit you need two extra pieces of software on your Windows machine. These are not included in the download, so get them ready first:

1. **hashcat** — the tool that tests password guesses against hashes. It is free, widely used, and runs on Windows.
2. **A text editor that handles big files** — Notepad++ or VS Code work well. The default Windows Notepad can struggle with files this large.

You should also have the file you want to audit: a set of password hashes exported from your Active Directory environment. If you are just practicing, use hashes from a lab or training environment rather than anything real.

---

## 📝 Step-by-Step: Using the Wordlist

These steps assume you have already visited the download link above and saved the file to your computer.

**Step 1 — Create a working folder.**
Make a folder such as `C:\audit\` and move the downloaded wordlist into it. Keep paths short and free of spaces; some command-line tools get confused by long paths with spaces in them.

**Step 2 — Check the file.**
Right-click the file and choose *Properties* to confirm the size looks right. Then open it in Notepad++ or VS Code to confirm it is plain text with one entry per line. Close it again before running any tool — a file that is open in an editor can be locked.

**Step 3 — Locate your hash file.**
Place the exported hash file in the same working folder so everything sits together. Give it a simple name like `hashes.txt`.

**Step 4 — Open a command prompt.**
Press the Windows key, type `cmd`, and open *Command Prompt*. Then move into your working folder by typing:

```
cd C:\audit
```

**Step 5 — Run a first pass.**
Point hashcat at the wordlist and the hash file. A straight dictionary run without rules is the fastest way to see results:

```
hashcat -m 13100 hashes.txt wordlist.txt
```

The `-m 13100` part tells hashcat the hash type is Kerberoast (Kerberos TGS-REP, RC4). If your export uses a different Kerberos encryption type, use the matching mode number instead.

**Step 6 — Read the results.**
When the run finishes, hashcat reports how many hashes were recovered. Those are the accounts that need a password reset today. Any account that survived the full run is comparatively strong.

---

## ⚡ Using It With hashcat and OneRuleToRuleThemStill

The real strength of this list shows up when you pair it with the rule file the repository is designed around.

A rule file is a small text file full of transformation instructions. Each line tells hashcat how to bend a base word: capitalise the first letter, append a year, swap `a` for `@`, add `!` at the end, and so on. One base word becomes dozens of realistic candidates.

Download `OneRuleToRuleThemStill.rule`, put it in the same folder, and add it to your command:

```
hashcat -m 13100 hashes.txt wordlist.txt -r OneRuleToRuleThemStill.rule
```

A practical audit order looks like this:

1. **Plain dictionary run** — no rules. Fastest, catches the obvious weak passwords.
2. **Rule-based run** — same list, same hashes, with the rule file applied. Catches lazy variations.
3. **Longer run** — let it finish overnight if the hash set is large.

This staged approach tells you something useful: passwords that fall in stage one are critically weak, passwords that fall in stage two are weak, and passwords that survive all three are the ones you can leave alone for now.

---

## 🔍 Common Questions

**Do I need to be a programmer to use this?**
No. If you can open a command prompt and paste a line of text, you can run a basic audit. Everything above is copy-and-paste ready.

**Is this a program I install?**
No. It is a text file of password guesses. The tools that use it — hashcat and similar — are separate downloads.

**How big is the list?**
It is large enough that opening it in the default Windows Notepad may be slow. That is normal. Use a proper editor.

**Can I rebuild it myself?**
Yes, and the repository deliberately supports it. The description spells out the recipe: merge HashMob large with `kerberoast_pws` using priority ordering, then run `rling` to deduplicate while preserving order. If you do not have `rling` available, installing it via a package manager on Linux or WSL is the usual route.

**Does the order of the list matter?**
Very much. Merging with priority and deduplicating with order preserved is the whole point — it keeps the most likely guesses at the top so your first minutes of cracking are the most productive.

**Will this work outside Active Directory?**
The list is tuned for AD and Kerberoasting, but nothing stops you from using it against other hash types. Just change the hashcat mode number to match what you are testing.

---

## 🛠️ Troubleshooting

**"The system cannot find the file specified."**
Your command prompt is not sitting in the folder that contains the wordlist. Fix it with `cd C:\audit` — replace the path with wherever you actually saved the files.

**The run is extremely slow.**
Rule-based runs are much heavier than plain dictionary runs. Close other programs, and be aware that the first pass without rules will always be the quickest way to a result.

**The editor freezes when opening the file.**
The list is big. Use Notepad++ or VS Code, or simply avoid opening it at all — you never need to read it to use it.

**hashcat says the hash mode is wrong.**
Your export uses a different Kerberos encryption type than the example. Check your export settings and use the hashcat mode number that matches.

**Nothing is being recovered at all.**
Confirm the hash file and the wordlist are both in the working folder and that you are passing the correct filenames on the command line.

---

## 🔐 Staying Legal and Ethical

Only run password audits against systems you own or have explicit written permission to test. Running these techniques against systems without authorisation is illegal in most jurisdictions and a fast way to end a career. In a corporate setting, get sign-off from whoever owns the environment before you start, and handle any recovered passwords as sensitive data.

---

## 📄 License

Released under the **MIT license**. You are free to use, modify, and redistribute it, including commercially, provided the license notice is preserved. The list is assembled from community sources, so credit to HashMob and The-Viper-One belongs wherever it is reused.

---

Keywords: active-directory, active-directory-security, dictionary-attack, hashcat, infosec, kerberoasting, kerberos, password-audit, password-cracking, pentesting, red-team, redteam-tools, security-audit, security-tools, wordlist, wordlist-generator