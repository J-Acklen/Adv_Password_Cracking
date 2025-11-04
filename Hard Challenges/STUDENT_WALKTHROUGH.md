# Password Cracking Lab - Student Walkthrough
## Easy Mode: CyberTech Solutions Breach

---

## 📖 Scenario

You are a security analyst investigating a breach at **CyberTech Solutions**. During your investigation, you've recovered password hashes from their system. Your job is to crack these passwords to assess the damage and identify which accounts were compromised.

**Company Intelligence:**
- Company Name: CyberTech Solutions
- Founded: 2018
- CEO: Sarah Mitchell
- Products: CloudGuard, NetShield
- Office Locations: Boston, Austin
- Industry: Cybersecurity

---

## 🎯 Your Mission

Crack the 10 password hashes in `challenge4.txt` using the custom wordlist `challenge4_wordlist.txt`

**Files Provided:**
- `challenge4.txt` - Contains 10 NTLM password hashes
- `challenge4_wordlist.txt` - Custom wordlist based on company information

---

## 📋 Step-by-Step Walkthrough

### Step 1: Verify Your Files

First, make sure you have both files in your current directory:

```bash
ls -l challenge4.txt challenge4_wordlist.txt
```

**Expected Output:**
```
-rw-r--r-- 1 user user  490 Nov  4 12:00 challenge4.txt
-rw-r--r-- 1 user user  250 Nov  4 12:00 challenge4_wordlist.txt
```

### Step 2: Examine the Hash File

Let's look at what we're trying to crack:

```bash
cat challenge4.txt
```

**You should see:**
```
AAD3B435B51404EE:F5237540D8A027BA5C3264302715ED4F
AAD3B435B51404EE:700867A58C9668BFC1D18E306D1CA672
AAD3B435B51404EE:78D1B73A0D5A9BDC397CE1F1DB89EFDF
... (10 lines total)
```

**Format Explanation:**
- `AAD3B435B51404EE` = LM hash (disabled/empty in modern Windows)
- `:` = Separator
- `F5237540D8A027BA5C3264302715ED4F` = NTLM hash (this is what we crack!)

### Step 3: Check the Wordlist

Let's see what words we have to work with:

```bash
cat challenge4_wordlist.txt
```

**You should see company-related terms like:**
- Company name variations (cybertech, CyberTech)
- Products (cloudguard, CloudGuard, netshield, NetShield)
- Locations (boston, Boston, austin, Austin)
- Employee names (sarah, Sarah, mitchell, Mitchell)
- Common words (security, welcome, password, admin)
- Years and combinations (2024, 2023, boston2024, cybertech2024)

### Step 4: Count the Wordlist Entries

Let's see how many potential passwords we have:

```bash
wc -l challenge4_wordlist.txt
```

**Expected Output:**
```
35 challenge4_wordlist.txt
```

Only 35 words! This is much smaller than rockyou.txt (14+ million words).

### Step 5: Run John the Ripper

Now let's crack the passwords using our custom wordlist:

```bash
john --format=nt --wordlist=challenge4_wordlist.txt challenge4.txt
```

**Expected Output:**
```
Using default input encoding: UTF-8
Loaded 10 password hashes with no different salts (NT [MD4 128/128 AVX 4x3])
Press 'q' or Ctrl-C to abort, almost any other key for status
CyberTech        (?)
cloudguard       (?)
Boston           (?)
netshield2024    (?)
austin2023       (?)
Sarah            (?)
Welcome          (?)
Password         (?)
cybertech2024    (?)
Security         (?)
10g 0:00:00:00 DONE (2024-11-04 12:00) 100.0g/s 3500p/s 3500c/s 35000C/s cybertech..cybertech2024
Use the "--show --format=NT" options to display all of the cracked passwords reliably
Session completed
```

**🎉 Success! All 10 passwords cracked!**

### Step 6: View the Cracked Passwords

To see all the cracked passwords clearly:

```bash
john --show --format=nt challenge4.txt
```

**Expected Output:**
```
AAD3B435B51404EE:CyberTech
AAD3B435B51404EE:cloudguard
AAD3B435B51404EE:Boston
AAD3B435B51404EE:netshield2024
AAD3B435B51404EE:austin2023
AAD3B435B51404EE:Sarah
AAD3B435B51404EE:Welcome
AAD3B435B51404EE:Password
AAD3B435B51404EE:cybertech2024
AAD3B435B51404EE:Security

10 password hashes cracked, 0 left
```

---

## 🔍 Analysis Questions

Now that you've cracked all the passwords, answer these questions:

### 1. **Pattern Recognition**
Look at the cracked passwords. What patterns do you notice?

<details>
<summary>Click to reveal analysis</summary>

- **Company-related:** CyberTech, cloudguard, netshield2024, cybertech2024
- **Location-based:** Boston, austin2023
- **Name-based:** Sarah (CEO's first name)
- **Generic weak:** Welcome, Password, Security

**Key Finding:** All passwords are directly related to company information!
</details>

### 2. **Why Did This Work?**
Why were we able to crack 100% of the passwords with such a small wordlist?

<details>
<summary>Click to reveal answer</summary>

The passwords were **predictable** because:
- Employees used company-specific terms
- They followed obvious patterns (company name + year)
- They used easily guessable information (product names, locations)
- No complex variations or randomness

This is why custom wordlists based on OSINT are so effective!
</details>

### 3. **Comparison: Custom vs. RockyYou**
How does this compare to using rockyou.txt?

<details>
<summary>Click to reveal answer</summary>

**Custom Wordlist (challenge4_wordlist.txt):**
- Only 35 words
- 100% success rate
- Completed in < 1 second

**RockyYou.txt:**
- 14+ million words
- Would take much longer
- Might miss company-specific terms like "netshield2024"

**Lesson:** Targeted, intelligence-based wordlists are often MORE effective than massive generic ones!
</details>

---

## 💡 Key Takeaways

✅ **Custom wordlists beat generic ones** when you have context
✅ **OSINT (Open Source Intelligence)** helps build effective wordlists
✅ **Predictable patterns are exploitable** (company name + year)
✅ **Small targeted lists** can be more effective than huge generic lists
✅ **Context matters** in password cracking

---

## 🚀 Challenge Yourself (Optional)

If you finish early, try these advanced exercises:

### Challenge A: Use RockyYou.txt
Try cracking with rockyou.txt instead:

```bash
john --format=nt --wordlist=/path/to/rockyou.txt challenge4.txt
```

**Question:** How many did it crack? Did it find "netshield2024"?

### Challenge B: Try Hashcat
Convert to hashcat format and try:

```bash
# Extract just NTLM hashes
cut -d':' -f2 challenge4.txt > ntlm_only.txt

# Crack with hashcat
hashcat -a 0 -m 1000 ntlm_only.txt challenge4_wordlist.txt

# Show results
hashcat -m 1000 --show ntlm_only.txt
```

### Challenge C: Build Your Own Wordlist
Research a different fictional company and build a custom wordlist. What terms would you include?

---

## 🛠️ Troubleshooting

### Problem: "No hashes loaded"
**Solution:** Check that you're using `--format=nt` and the file format is correct (LM:NTLM with colons)

### Problem: "Wordlist not found"
**Solution:** Make sure the file path is correct. Try `./challenge4_wordlist.txt` or use the full path

### Problem: "0 passwords cracked"
**Solution:** 
- Verify you're using the right wordlist file
- Check that the wordlist contains the actual passwords
- Make sure there are no extra spaces or line ending issues

### Problem: John shows "Already loaded"
**Solution:** John remembers previously cracked passwords. Use `--show` to see them:
```bash
john --show --format=nt challenge4.txt
```

---

## 📊 Expected Results

By the end of this lab, you should have:
- ✅ Cracked all 10 passwords (100% success rate)
- ✅ Understood why custom wordlists are effective
- ✅ Learned how to use John the Ripper for NTLM hashes
- ✅ Recognized password patterns in organizations
- ✅ Understood the importance of OSINT in password cracking

---

## 🎓 Discussion Points

**For Class Discussion:**

1. **Why did employees choose these passwords?**
   - Easy to remember
   - Company loyalty/pride
   - Followed password policy (length, maybe capitals)
   - But: easily guessable!

2. **What makes a password secure?**
   - NOT just complexity (Password! vs. correct-horse-battery-staple)
   - Unpredictability (high entropy)
   - No personal or company information
   - Long and random

3. **How can companies prevent this?**
   - Password managers (generate random passwords)
   - Blacklist company-related terms
   - Multi-factor authentication (MFA)
   - Password complexity rules (but not just symbol requirements!)
   - Regular password audits

4. **Real-world implications:**
   - Attackers DO build custom wordlists
   - LinkedIn, company websites, social media = OSINT goldmine
   - One weak password = potential breach

---

## ✅ Lab Complete!

Congratulations! You've successfully:
- Cracked 10 NTLM password hashes
- Used a custom wordlist based on company intelligence
- Learned why contextual password cracking is so effective
- Understood the importance of unpredictable passwords

**Next Steps:**
- Try the medium or hard mode challenges
- Practice building wordlists from real company websites (ethically!)
- Learn about rule-based attacks for more complex passwords

---

## 📚 Additional Resources

**Tools:**
- John the Ripper: https://www.openwall.com/john/
- Hashcat: https://hashcat.net/hashcat/
- CeWL (Custom Wordlist Generator): https://github.com/digininja/CeWL

**Learning:**
- NTLM Hash Format: https://en.wikipedia.org/wiki/NT_LAN_Manager
- Password Cracking Techniques: https://www.openwall.com/john/doc/
- OSINT for Security: Search for "OSINT framework"

---

**Lab Created By:** [Your Name]
**Date:** November 4, 2024
**Difficulty:** Easy (Beginner-Friendly)
**Time Required:** 15-20 minutes
