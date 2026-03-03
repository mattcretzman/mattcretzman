# MDX Post Template

## Frontmatter Structure

```yaml
---
title: "[Title - 50-60 chars, include primary keyword early]"
slug: "[URL-friendly slug, primary keyword focused]"
description: "[Meta description - 150-160 chars, include primary keyword + CTA]"
date: "[ISO 8601 date, e.g., 2026-02-24]"
author: "Matt Cretzman"
tags: ["[tag1]", "[tag2]", "[tag3]", "[tag4]"]
category: "[Category matching content type]"
featured: false
coverImage: "/images/[slug]-cover.png"
seo:
  keywords: ["[primary]", "[secondary-1]", "[secondary-2]", "[secondary-3]"]
  ogImage: "/images/[slug]-og.png"
readingTime: [number] minutes
ventures: ["[Related venture names from queue]"]
---
```

## Content Structure

### 1. Opening Hook (100-150 words)
- Start with a contrarian take, surprising stat, or relatable pain point
- Include primary keyword naturally in first 100 words
- End with "Here's what I learned..." or similar transition

### 2. Table of Contents (auto-generated via MDX)
```mdx
## Table of Contents

1. [Section Name](#section-slug)
2. [Section Name](#section-slug)
3. [Section Name](#section-slug)
```

### 3. Main Content Sections

Each section should:
- Have H2 heading with slugified anchor
- Include primary or secondary keyword in at least 2 section headings
- Be 300-600 words
- Include one of: real example, data point, or actionable insight
- End with transition to next section

### 4. Real Examples / Case Studies
- Always use real implementations (Matt's own projects)
- Include specific numbers when possible (conversion rates, time saved, ROI)
- Format as:
```mdx
### Example: [Project Name]

**The Challenge:** [1-2 sentences]

**The Implementation:** [3-4 sentences]

**The Results:**
- Metric 1: X% improvement
- Metric 2: Y hours saved
- Metric 3: Z outcome
```

### 5. Technical Implementation (when relevant)
- Include code blocks with syntax highlighting
- Keep code concise but complete enough to be useful
- Add comments explaining key decisions

### 6. Common Mistakes Section
- List 2-4 mistakes people make
- Explain why each hurts results
- Provide correction for each

### 7. Key Takeaways / Summary
- Bullet list of 4-6 actionable insights
- Each should be tweetable (under 280 chars)

### 8. Call to Action
- Context-aware based on target ventures
- Specific next step, not generic "learn more"
- Link to relevant product/service when applicable

## Cross-Linking Rules

- Link to other Matt Cretzman properties naturally
- Use descriptive anchor text (not "click here")
- Maximum 3 external links per post
- Link to 1-2 related internal posts if they exist

## SEO Checklist

- [ ] Primary keyword in H1 (title)
- [ ] Primary keyword in first 100 words
- [ ] Primary keyword in at least 1 H2
- [ ] Secondary keywords in H2s or H3s
- [ ] Meta description includes primary keyword
- [ ] URL slug matches primary keyword
- [ ] Image alt text includes keywords
- [ ] Internal links use descriptive anchor text
- [ ] Reading time accurate (target: 5-8 min for 2000 words)

## Tone & Voice

- Write as Matt Cretzman speaking to a peer
- Use "I" and "we" when referring to implementations
- Be direct — no fluff
- Include occasional personality (cigar references, building metaphors)
- Never hype — let results speak
- Technical depth without gatekeeping

## Formatting

- Use **bold** for emphasis and key terms
- Use `code` for technical terms, tools, file names
- Use bullet lists for scanability
- Use numbered lists for sequential steps
- Maximum 4 lines per paragraph
- Include line breaks between sections

## MDX Components Available

```mdx
<Callout type="info|warning|success|error">
  Highlighted information block
</Callout>

<CodeExample language="javascript">
  {`const example = 'code';`}
</CodeExample>

<Quote author="Name" source="Source">
  Pull quote from content
</Quote>

<Image 
  src="/images/example.png" 
  alt="Descriptive alt text with keywords"
  caption="Optional caption"
/>
```
