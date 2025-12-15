facts:**

- Asterisks for bold/italic: `*emphasis*` or `**strong**`
- Underscores for emphasis: `_italic_`
- Hash symbols for headers: `## Section Title`
- Backticks for code: `` `inline code` ``
- Triple backticks: ```` ```code block``` ````
- Numbers with periods for lists when not rendered: `1. First item`

**Why this happens:** LLMs are trained to output Markdown (used on GitHub, Reddit, Discord, etc.) and sometimes don't translate correctly to the target platform's formatting.

---

### 16. Curly vs. Straight Quotes

**Pattern:** Inconsistent use of curly quotes (") versus straight quotes (") or the wrong type for the context.

**Why this signals AI:** Different training data and platforms use different quote styles. LLMs may insert curly quotes in contexts where straight quotes are standard, or vice versa.

---

### 17. Title Case in Headers

**Pattern:** Section headers capitalize every major word (Title Case) instead of using sentence case.

**Examples:**

- AI: "The Evolution Of Modern Technology"
- Human journalism: "The evolution of modern technology"

**Why this signals AI:** Many LLMs default to title case for headers because it's common in certain types of content (marketing, academic), but most journalism uses sentence case.

---

## Technical and Formatting Tells

### 18. Placeholder Text and Incomplete Elements

**Pattern:** Bracketed placeholders left in published content.

**Common examples:**

- `[Insert source here]`
- `[Add specific example]`
- `[URL of reliable source]`
- `[Citation needed]`
- `[Date]`

**Why this happens:** A user copies AI-generated text with placeholders they were supposed to fill in but forgot.

**Variation:** Sometimes appears as XML-like notation: `:contentReference[oaicite:0]`

---

### 19. Chatbot Communication Artifacts

**Pattern:** Text that includes meta-communication between the chatbot and user.

**Examples:**

- Salutations: "Dear [Reader]," "Hello!"
- Valedictions: "Thank you for your time and consideration," "I hope this helps!"
- Instructions to user: "Here is your article on [topic]"
- Knowledge cutoff disclaimers: "As of my last training update in [date]..."
- Disclaimers: "Please consult a professional before..."
- Offers to assist further: "If you have any questions or need further clarification, feel free to ask!"

**Why this is a strong tell:** These phrases reveal that content was generated in response to a prompt and copied without editing.

---

### 20. Broken or Fabricated Links and Technical Codes

**Pattern:** Links, DOIs, ISBNs, or other technical identifiers that don't resolve or are invalid.

**Common issues:**

- URLs that lead to 404 errors
- DOIs that don't resolve to any article
- ISBNs with invalid checksums
- Generic placeholder links: `[Link to source]`
- Since February 2025: ChatGPT-specific artifacts like "turn0search0"

**Why this happens:** LLMs hallucinate (fabricate) citations that look credible but don't actually exist.

**Detection method:** Click links, verify DOIs resolve, check ISBNs with checksum validators.

---

### 21. Citation Abnormalities

**Pattern:** References that appear legitimate but reveal AI generation upon inspection.

**Common issues:**

- Citations repeated multiple times without proper reference tagging
- Real sources cited for completely unrelated content
- Citations formatted in unusual or inconsistent styles
- Multiple citations to the same source without variation in attribution
- Generic citations: "According to experts..." without naming the experts

**Example of suspicious pattern:** Multiple identical citations in close proximity rather than using a single citation or cross-referencing.

---

### 22. Suspiciously Long or Elaborate Edit Summaries

**Pattern:** In platforms with edit tracking, unusually long, formal edit summaries written in first-person paragraphs.

**Example:**

"Refined the language of the article for a neutral, encyclopedic tone consistent with content guidelines. Removed promotional wording,