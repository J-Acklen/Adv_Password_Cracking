# Password Cracking Lab - Student Walkthrough (UPDATED)
## Medium Mode: CyberTech Solutions Breach - Rule-Based Attack Required

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

**⚠️ Important Note:** This wordlist has been refined to contain only BASE WORDS (no combinations). You'll need to use **rule-based attacks** to crack all passwords!

---

## 📋 Part 1: Basic Dictionary Attack (The Limitation)

### Step 1: Verify Your Files

Make sure you have both files:

```bash
ls -l challenge4.txt challenge4_wordlist.txt
```

### Step 2: Check the Wordlist

Let's see what we're working with:

```bash
cat challenge4_wordlist.txt
```

**Notice:** The wordlist contains individual words and years separately:
- `cybertech`, `CyberTech` (but NOT `cybertech2024`)
- `boston`, `Boston` (but NOT `boston2024`)
- `2024`, `2023`, `2022` (years by themselves)

**Count the words:**
```bash
wc -l challenge4_wordlist.txt
```

You should see **30 words** (down from 35 in the easy version).

### Step 3: Run Basic Dictionary Attack

Let's try a simple dictionary attack first:

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
Sarah            (?)
Welcome          (?)
Password         (?)
Security         (?)
7g 0:00:00:00 DONE (2024-11-04 12:00) 100.0g/s 3000p/s 3000c/s 30000C/s cybertech..2022
Session completed
```

### Step 4: View Partial Results

```bash
john --show --format=nt challenge4.txt
```

**You Should See:**
```
AAD3B435B51404EE:CyberTech
AAD3B435B51404EE:cloudguard
AAD3B435B51404EE:Boston
AAD3B435B51404EE:Sarah
AAD3B435B51404EE:Welcome
AAD3B435B51404EE:Password
AAD3B435B51404EE:Security

7 password hashes cracked, 3 left
```

### 🤔 Analysis: What Happened?

**✅ Cracked (7 passwords):**
- CyberTech
- cloudguard
- Boston
- Sarah
- Welcome
- Password
- Security

**❌ NOT Cracked (3 passwords):**
- ??? (still unknown)
- ??? (still unknown)
- ??? (still unknown)

**Why didn't they crack?**

<details>
<summary>Click to reveal</summary>

The remaining passwords are likely **COMBINATIONS** of base words + years:
- Possibly: `netshield2024` (netshield + 2024)
- Possibly: `austin2023` (austin + 2023)
- Possibly: `cybertech2024` (cybertech + 2024)

Our wordlist has `netshield` and `2024` **separately**, but not `netshield2024` as a single word.

**This is where RULES come in!** 🎯
</details>

---

## 📋 Part 2: Rule-Based Attack (The Solution)

Rules allow John to automatically modify wordlist entries by:
- Appending numbers (netshield → netshield2024)
- Capitalizing letters (cyber → Cyber)
- Adding special characters (password → password!)
- And much more...

### Step 5: Apply Rules to Crack Remaining Passwords

Try John's built-in rules:

```bash
john --format=nt --wordlist=challenge4_wordlist.txt --rules=best64 challenge4.txt
```

**Expected Output:**
```
Using default input encoding: UTF-8
Loaded 10 password hashes with no different salts (NT [MD4 128/128 AVX 4x3])
Remaining 3 password hashes with no different salts
Press 'q' or Ctrl-C to abort, almost any other key for status
netshield2024    (?)
austin2023       (?)
cybertech2024    (?)
3g 0:00:00:01 DONE (2024-11-04 12:01) 2.500g/s 52000p/s 52000c/s 156000C/s
Use the "--show --format=NT" options to display all of the cracked passwords reliably
Session completed
```

**🎉 Success! The remaining 3 passwords cracked with rules!**

### Step 6: View All Cracked Passwords

```bash
john --show --format=nt challenge4.txt
```

**Final Results:**
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

**✅ All 10 passwords cracked!**

---

## 🔍 Analysis Questions

### Question 1: What's the Difference?

Compare the two phases:

| Phase | Attack Type | Passwords Cracked |
|-------|-------------|-------------------|
| Phase 1 | Basic Dictionary | 7/10 (70%) |
| Phase 2 | Rule-Based | 10/10 (100%) |

**Why did Phase 2 work?**

<details>
<summary>Click to reveal</summary>

**Phase 1** only matched words exactly as they appear in the wordlist.
- ✅ `Boston` in wordlist → `Boston` password = MATCH
- ❌ `netshield` in wordlist → `netshield2024` password = NO MATCH

**Phase 2** used rules to generate variations:
- `netshield` + append "2024" → `netshield2024` ✅
- `austin` + append "2023" → `austin2023` ✅
- `cybertech` + append "2024" → `cybertech2024` ✅

**Key Insight:** Rules MULTIPLY the effectiveness of your wordlist!
</details>

### Question 2: Which Passwords Required Rules?

Look at the cracked passwords. Which 3 needed rules to crack?

<details>
<summary>Click to reveal</summary>

The three that required rules were:
1. **netshield2024** (netshield + 2024)
2. **austin2023** (austin + 2023)
3. **cybertech2024** (cybertech + 2024)

**Pattern:** All three are base words + years appended
</details>

### Question 3: What Rules Were Applied?

What transformations did John make to crack those 3?

<details>
<summary>Click to reveal</summary>

Using `--rules=best64`, John applied transformations like:
- **Append digits:** `netshield` → `netshield2024`
- **Append years:** `austin` → `austin2023`
- **Lowercase + append:** `cybertech` → `cybertech2024`

The `best64` ruleset includes 64 of the most common password mutations.
</details>

### Question 4: Why Not Just Add All Combinations to the Wordlist?

Why didn't we just include `netshield2024`, `austin2023`, etc. in the wordlist?

<details>
<summary>Click to reveal</summary>

**Problems with pre-combined wordlists:**
- Exponential growth: 10 words × 10 years = 100 combinations
- 100 words × 100 variations = 10,000 entries
- With special characters and capitalization = millions of entries

**Benefits of rules:**
- Small wordlist (30 words)
- Rules generate variations on-the-fly
- More flexible and efficient
- Can adjust rules for different scenarios

**Best Practice:** Small targeted wordlist + powerful rules > massive wordlist
</details>

---

## 💡 Understanding Rules

### What Rules Did to Your Wordlist

**Original wordlist entry:**
```
netshield
```

**After applying `best64` rules, John tried:**
```
netshield
Netshield
NETSHIELD
netshield1
netshield2
netshield123
netshield2024
netshield2023
netshield!
Netshield1
... (64 variations total)
```

**Result:** `netshield2024` matched! ✅

### Common Rule Types

John's rules can:
- **Append:** Add characters to the end (`$2$0$2$4` = append "2024")
- **Prepend:** Add characters to the start
- **Capitalize:** Change case (`c` = capitalize first letter)
- **Leetspeak:** Replace letters (`a → @`, `e → 3`)
- **Duplicate:** Repeat characters
- **Reverse:** Flip the word
- **And more...**

---

## 🎯 Key Takeaways

### Lesson 1: Dictionary Attacks Have Limits
✅ Basic dictionary: 70% success (7/10)
❌ Simple approach misses variations

### Lesson 2: Rules Multiply Effectiveness
✅ Rule-based attack: 100% success (10/10)
✅ 30-word wordlist → thousands of attempts with rules

### Lesson 3: Patterns Are Predictable
❌ Base word + year = common pattern
❌ Attackers know this pattern
❌ Rules exploit this predictability

### Lesson 4: Real-World Application
✅ Attackers use OSINT to build wordlists
✅ They apply rules to generate variations
✅ This is how real breaches happen

---

## 🚀 Optional Challenges

### Challenge A: Try Different Rules

John has many rule sets. Try these:

```bash
# KoreLogic rules (very comprehensive)
john --format=nt --wordlist=challenge4_wordlist.txt --rules=KoreLogic challenge4.txt

# Jumbo rules (includes everything)
john --format=nt --wordlist=challenge4_wordlist.txt --rules=jumbo challenge4.txt

# Single rule (very aggressive)
john --format=nt --wordlist=challenge4_wordlist.txt --rules=single challenge4.txt
```

### Challenge B: List Available Rules

See what rules John has:

```bash
john --list=rules
```

### Challenge C: Understand Specific Rules

View what `best64` actually does:

```bash
john --list=rules:best64 | head -20
```

**You'll see rules like:**
- `$2$0$2$4` = Append "2024"
- `$1$2$3` = Append "123"
- `c` = Capitalize first letter
- `$!` = Append "!"

### Challenge D: Manual Rule Testing

Test rules manually with `--stdout`:

```bash
echo "netshield" | john --wordlist=- --rules=best64 --stdout | grep 2024
```

**You should see:**
```
netshield2024
Netshield2024
... (any variations with 2024)
```

### Challenge E: Compare with Hashcat

Try the same attack with hashcat:

```bash
# Extract NTLM only
cut -d':' -f2 challenge4.txt > ntlm_only.txt

# Basic attack
hashcat -a 0 -m 1000 ntlm_only.txt challenge4_wordlist.txt

# With rules
hashcat -a 0 -m 1000 ntlm_only.txt challenge4_wordlist.txt -r /usr/share/hashcat/rules/best64.rule

# Show results
hashcat -m 1000 --show ntlm_only.txt
```

---

## 🛠️ Troubleshooting

### Problem: No passwords cracking in Phase 2
**Solution:** Make sure you're using `--rules`:
```bash
john --format=nt --wordlist=challenge4_wordlist.txt --rules=best64 challenge4.txt
```

### Problem: "No hashes loaded" in Phase 2
**Solution:** John already cracked them in Phase 1! Use `--show` to see all results:
```bash
john --show --format=nt challenge4.txt
```

### Problem: Want to start fresh
**Solution:** Remove John's pot file (where cracked passwords are stored):
```bash
rm ~/.john/john.pot
# Now try again from Phase 1
```

### Problem: Rules seem slow
**Solution:** `best64` is fast. If you used `jumbo` or `all`, it can take longer. Stick with `best64` for this lab.

---

## 📊 Lab Summary

### What You Did:

**Phase 1: Basic Dictionary Attack**
- Command: `john --format=nt --wordlist=challenge4_wordlist.txt challenge4.txt`
- Result: 7/10 cracked (70%)
- Lesson: Dictionary alone isn't enough

**Phase 2: Rule-Based Attack**
- Command: `john --format=nt --wordlist=challenge4_wordlist.txt --rules=best64 challenge4.txt`
- Result: 10/10 cracked (100%)
- Lesson: Rules multiply wordlist effectiveness

### Final Password List:

**Simple passwords (Phase 1):**
1. CyberTech
2. cloudguard
3. Boston
4. Sarah
5. Welcome
6. Password
7. Security

**Complex passwords (Phase 2 - required rules):**
8. netshield2024 (netshield + 2024)
9. austin2023 (austin + 2023)
10. cybertech2024 (cybertech + 2024)

---

## 🎓 Discussion Points

**For Class Discussion:**

### 1. Why Do Employees Use Predictable Patterns?
- Easy to remember
- Think adding year makes it "complex"
- Don't realize attackers know these patterns

### 2. Are These "Complex" Passwords?
- They meet technical requirements (8+ characters, mix of cases)
- But they're still predictable!
- **Lesson:** Complexity ≠ Security

### 3. How Can Organizations Defend?
- Password managers (truly random passwords)
- Block common patterns (company name + year)
- Regular password audits
- Multi-factor authentication (MFA)
- User education

### 4. Real-World Attacker Workflow
1. **Reconnaissance:** Gather OSINT (company info, employee names)
2. **Wordlist Creation:** Build custom list from intel
3. **Rule Application:** Apply common patterns (+ years, + special chars)
4. **Crack:** Run attack with rules
5. **Profit:** Access compromised accounts

**This is exactly what you just did!**

---

## ✅ Lab Complete!

Congratulations! You've learned:
- ✅ How to use John the Ripper for NTLM hashes
- ✅ The difference between dictionary and rule-based attacks
- ✅ Why rules multiply wordlist effectiveness
- ✅ Common password patterns in organizations
- ✅ How attackers use OSINT + rules in real breaches
- ✅ Defense strategies against these attacks

**Key Insight:** Small targeted wordlist (30 words) + rules > massive generic wordlist (14M words)

---

## 📚 Additional Resources

**John the Ripper:**
- Official documentation: https://www.openwall.com/john/
- Rule syntax: https://www.openwall.com/john/doc/RULES.shtml
- Community rules: https://github.com/magnumripper/JohnTheRipper

**Password Cracking:**
- Hashcat: https://hashcat.net/hashcat/
- OSINT Framework: https://osintframework.com/
- Password statistics: https://haveibeenpwned.com/

**Security:**
- NIST Password Guidelines: https://pages.nist.gov/800-63-3/
- Password managers: Bitwarden, 1Password, LastPass
- MFA best practices

---

**Lab Created By:** [Your Name]
**Date:** November 4, 2024
**Difficulty:** Medium (Rule-Based)
**Time Required:** 20-30 minutes
