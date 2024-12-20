---
title: "Password Composition Policies Are Bad and Here's Why"
slug: "password-composition-policies-are-bad-and-heres-why"
tags: passwords, security, authentication
cover: https://raw.githubusercontent.com/Armadillidiid/blog-posts/main/password-composition-policies-are-bad-and-heres-why/cover.webp
---

Recently, I came across a [LinkedIn post](https://www.linkedin.com/posts/michael-lin-tech_never-blindly-copy-what-others-do-i-learned-activity-7264454532833640448-Po7m?utm_source=social_share_sheet&utm_medium=member_desktop_web) applauding how Netflix adopted a lax password creation policy pertaining to their specific business model and the worst consequence of a compromised password is your account will be used to **"watch a few movies"**. While the latter is obviously not true lol, it sparked my interest in writing this piece on strict password composition policies and how they often do more harm than good at creating secure passwords.
how i have a public folder in my github storing images. how do i access via url in my project?

Passwords are the most widely used form of authentication on the internet. And unless you're Nelson Dellis—a five-time USA Memory Champion—most of us tend to pick the easiest passwords we can remember when creating one.

I must admit, I've never been a big fan of passwords, especially with the availability of more secure authentication options like OAuth, magic links, and passkeys. Passwords inherently carry more security risks compared to other alternatives and should ideally be paired with Multi-Factor Authentication (MFA) to be considered truly secure.

Secure password generation is complicated by the tradeoff between developing passwords which are both easy to remember and difficult to crack. To help guard against the latter, password composition policies are enforced to coerce users to create secure passwords. However, they often fail to achieve their intended purpose.

I'm sure you've experienced this before: you go to a website's sign-up page, and you're prompted to create a password with minimum 8 characters, 1 uppercase letter, 1 lowercase letter, 1 number, and 1 special character. Next thing you know, you find yourself substituting letters for special characters(e.g, `a` with `@`) and appending numbers to the end of your password. This can be really frustrating because your password may be secure already.

Fortunately, in recent years, the rise of password managers has helped dampen this issue. We are now able to create long, random passwords for each site we visit and at the same store them securely. But password managers aren't widely adopted yet in the space, so the original issue still semi persists.

This matter of subject is luckily very well researched and has been discussed by many security experts, with the most notable being the National Institute of Standards and Technology (NIST). They have published guidelines on digital identity management, which one of them talks about password composition policies called [SP 800-63B](https://pages.nist.gov/800-63-4/sp800-63b.html). Not gonna lie--It's a long read. But it's definitely worth it, as it gets updated regularly to keep pace with latest findings.

Also there's a [published paper by Florida State University](https://doi.org/10.1145/1866307.1866327) on password creation policies where they test real leaked passwords from database breaches against composition policies to examine the result effect.

I'll be referencing both of these in this article to better explain to you why password composition policies are bad and what we better approaches we can take to encourage users to create secure passwords. It's also good note that I'll be focusing only on online environments for composition policies, not offline.

Without no further ado, let's dive in.

## Problem with Composition Policies and Entropy

Composition policies are rules that dictate what constitutes an acceptable password to the user before they proceed to create it. These rules typically include requirements like a minimum length, a mix of uppercase and lowercase letters, numbers, and special characters The goal is to make passwords more secure by increasing the entropy (more on that later). For example, a policy might mandate users include at least one digit in their password.

However, research consistently shows that users respond to these requirements in predictable ways when forced. In the [Florida State University research paper](https://doi.org/10.1145/1866307.1866327), after performing a dictionary attack on 14 million passwords from [RockYou](https://en.wikipedia.org/wiki/RockYou) database breach, researchers found that users often appended a digit to the end of an otherwise weak password. This highlights how composition policies can fail to deliver their intended security benefits. Here’s what they further had to say:

> "It shouldn’t come as a surprise that most people simply add digits
> to the end of a base word when creating a password. If we filter
> the training list to only include passwords that also contained one
> non-digit, the number of users who appended a digit to the end of
> their password jumped to 77.46%."

You can utilize [Have I Been Pwned](https://haveibeenpwned.com/API/v3) API to confirm this yourself if in doubt.

Another important observation is that the numbers users choose are often predictable. What do I mean? According to the same [Florida State University research paper](https://doi.org/10.1145/1866307.1866327), the digits “1” through “9” account for **36.25%** of the top 10 most common digits used in passwords, with **“1”** being the most frequent.

The above pattern extends to uppercase letters and special characters as well. Over **50%** of users capitalize the first letter of their password when required to include an uppercase character, while **36.37%** append a special character to the end of their password.

Interestingly, when composition policies require both a **special character** and a **digit**-—a common requirement in most policies—it was found that special characters are more often followed by a digit rather than the other way around.

This significantly reduces the key-space of possible passwords, making them easier to guess. In the event of an attack, a malicious actor would be fully aware of these constraints and their predictable effects. They would leverage this knowledge to reduce the number of possible guesses needed to crack the password.

Quoting NIST, here's what they have to say on composition policy:

> "Verifiers and CSPs SHALL NOT impose other composition rules (e.g., requiring mixtures of different character types) for passwords."
>
> **Note:** _Verifiers are entities that verify user credentials during the authentication process, while CSPs (Credential Service Provider) are the entities that manage and store the user's credentials._

It might not be obvious, but adding complexity doesn't out right reduce the guessability of the password. Yes, you’re correct—-it increases the entropy. But higher entropy doesn't equate to lower guessability. Why?

Well [Shannon's entropy](<https://en.wikipedia.org/wiki/Entropy_(information_theory)>) theoretically assumes outcomes are evenly distributed which isn't true for most real-world password distributions. For example, **"123456"** is far more common to be found in human-choosen password than, say, **"vH8$dq@"**.

> Shannon's Entropy is the measure of theoretical unpredictability of a password based on the possible combinations of characters.

So in essence, even if it's higher, it doesn't necessarily convey to us how secure a password is. It's like saying a 10-character password with 5 bits of entropy is more secure than an 8-character password with 10 bits of entropy. That's not always the case. For example, **"Password123$"** and **"ohgreatsully"** have both 41 bits of entropy when calculated, yet the latter is significantly less guessable in a dictionary attack and would take centuries to crack using brute-force.

Highly complex passwords once again introduce a new challenge: they're harder to remember, making users more likely to write them down or store them insecurely. While secure storage solutions like password managers exist, survey shows that only about **32% of people** (at best, based on data from a subset of countries) actually use them, according to Bitwarden's [World Password Day Survey 2024](https://bitwarden.com/resources/world-password-day/). With such low adoption rates, I would say this should be an extra motivation not to enforce long and complex passwords upfront.

Despite everything I've said so far, there's one exception to the rule: **the minimum length requirement**. It is the only useful recommended composition policy--provided it's used within reason.

The reason is simple: the number of possible combinations increases exponentially with the length of the password. For example, **a 10-character password** has 10^16 possible combinations, while a **20-character password** has 10^32 possible combinations.

Here's **NIST's** take on password length:

> "Verifiers and CSPs SHALL require passwords to be a minimum of eight characters in length and SHOULD require passwords to be a minimum of 15 characters in length."

Password length is a primary factor in characterizing password strength, so users should be encouraged to make their passwords as lengthy as they want. The more characters in a password, the more secure it is. Too short would put the users at severe risk to brute-force and dictionary attacks.

## A Better Approach

So far, we're looked at **what not to do**. Now, let's discuss **what to do** to improve password security without the need for composition policies:

### Check Passwords Against a Blacklist

> "...verifiers SHALL compare the prospective secret against a blocklist that contains known commonly used, expected, or compromised passwords"

> "If the chosen password is found on the blocklist, the CSP or verifier SHALL require the subscriber to select a different secret and SHALL provide the reason for rejection"

One of the most effective ways to improve password security is to check passwords against a blacklist. This typically includes:

- Compromised passwords from previous database breaches.
- Common dictionary words.
- Context-specific words, such as the name of the service or platform.

If a password matches an entry on the blacklist, it should be rejected, and the user should be prompted to choose a different password, with a clear reason for rejection provided.

According to one of the test cases carried out in the [Florida research paper](), a blacklist of 50,000 entries was tested against 32 million compromised passwords with a maximum of 256 guesses. The results showed that the percentage pool of passwords found to be crackable **with the blacklist was 0.058%**, compared to **up to 14% without any blacklist**.

Second shoutout to [Have I Been Pwned API](https://haveibeenpwned.com/API/v3). They're a reliable way to source up-to-date list of compromised passwords for your blacklist from latest security database breaches.

In as much as blacklist is great, we're only able to compare the entire string, not substrings or words that might be contained therein. This is a hard limitation but there's more that could done to circumvent this.

In as much as blacklists are incredibly useful, they come with a hard limitation: they can only compare entire strings, not substrings or individual words contained within a password. This limitation makes it impossible to detect sub-patterns.

But wait--there’s more that could be done!

### Evaluate Password Strength, not Complexity

Building upon blacklists, we can take it a step further by creating a function to dynamically evaluate the guessability of the password based on multiple factors such as length, dates (e.g, birthdays), repeated characters (e.g, "aaaaaa"), sequences (e.g, "abcdef", "1233456"), spatial words based keyboard layout(e.g,"qwerty"), leet-speak (e.g, "H3ll0" => "Hello") and brute-force weakness. We take all these factors into account and assign a guessability score to the password. With this score, we can reject any password whose guessability falls below a set threshold.

What’s more, we can even randomly suggest a new password with a higher guessability and educate users as well about what constitutes a strong password.

You might be thinking to yourself--"That's sound like a lot of work". And you're dead right. But fortunately for us, some fantastic engineers at Dropbox created a tool to solve this very issue a while back called [zxcvbn](https://github.com/dropbox/zxcvbn). Albeit, it's now old and unmaintained, there's a fork implementation for every programming language out there. For JavaScript users, you can check out [zxcvbn-ts](https://github.com/zxcvbn-ts/zxcvbn).

Here's what NIST has to say on password strength estimation:

> Verifiers SHALL offer guidance to the subscriber to assist the user in choosing a strong password.

Effectively, with the guessability data from our password strength estimator of choice, we can use that response to create strength meters, alert cards, etc. to guide the user into creating more secure passwords.

## Password Strength Estimator Demo

To demo estimating password strength, I built a web app that lets you compare composition policies against guessability metrics using [zxcvbn-ts](https://github.com/dropbox/zxcvbn) and [Have I Been Pwned API](https://haveibeenpwned.com/API/v3). You can [check it out here](https://password-strength-checker-eta-navy.vercel.app).

Upon inputting your password, you'll get to see its guessability score, estimated time to crack, pattern matches, and whether it has been compromised. You can find the [source code here](https://github.com/Armadillidiid/password-strength-checker).

As shown in the screenshot below, `P@ssword1'` meets all composition policy requirements but returns a terrible guessability metric. Go ahead and give it a try for yourself.

![`P@ssword1` Strength Result](https://raw.githubusercontent.com/Armadillidiid/blog-posts/main/password-composition-policies-are-bad-and-heres-why/screenshot-password-strength-checker.png)

## Conclusion

Hopefully, I was able to demonstrate to you that the concept of password entropy, as commonly applied by the most websites (through composition policies), fails to serve as a reliable metric for assessing the security of password.

While these composition policies are well-intentioned, they often fall short of their goal. A significant subset of users will still choose easy-to-guess passwords, like `P@ssword1`, that technically comply with the policy but remain highly vulnerable to attackers.

It's time we move past strict password composition policies--they're not helping anyone. We've seen that it's possible to improve password security without sacrificing user experience.

If you really think it's not enough, you can also consider implementing Multi-Factor Authentication (MFA). That way even if a password is compromised, the attacker can't gain access without the second factor.

Thanks for reading! I hope you found this article helpful. Feel free to leave a comment if you have any further questions or suggestions.
