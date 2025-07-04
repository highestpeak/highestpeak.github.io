---
title: Introduction to Online Collaboration Document System
date: 2024-12-09T21:00:33+08:00
draft: false
summary: As the opening article of this series, I clarify my purpose and background for writing it and explain why I chose to focus on technical sharing about online document and spreadsheet systems. I review my accumulated project experience and emphasize that the content is intended for readers with varying levels of expertise. Through this article, I hope readers can understand the starting point of my writing and prepare for the upcoming content, while also organizing and summarizing years of technical work experience. This article lays a foundation for deeper technical discussions and helps readers grasp the positioning and direction of the entire series.
tags:
  - collaboration
  - system
  - index
categories:
  - collaboration
featuredImage: https://cdn.jsdelivr.net/gh/highestpeak/public-image@master//4B594769-CCFB-4793-B9E0-5E6FA8AC9DDC_1_105_c.jpeg
featuredImagePreview: https://cdn.jsdelivr.net/gh/highestpeak/public-image@master//4B594769-CCFB-4793-B9E0-5E6FA8AC9DDC_1_105_c.jpeg
---

## Starting Point

As I write these words, it is already December 9, 2024. With my upcoming move to New Zealand for study in March 2025—flying out in February and leaving my current job by the end of January—my preparation time is extremely tight.

I submitted my student visa application just after midnight on December 5, 2024. In the past few days, while preparing for accommodation and packing, I have decided to make full use of the limited time ahead to write this series of articles.

Once the visa is granted, I plan to widely seek advice from colleagues and friends to further polish the series and strive for excellence. (Though in fact, I haven’t really done that, haha 😆)

### Why write

Why do I want to write this series about document and online spreadsheet systems? The reasons are as follows:

1. My resignation may attract some attention; these people could become potential users of future products and help cultivate a seed market.
2. The articles can serve as supporting materials showcasing real project experience, which is very persuasive for job hunting in New Zealand or other countries, and also helps strengthen ties with industry friends.
3. This is also strong evidence of my professional accumulation for future career development, whether domestically or internationally.
4. I have worked on this project for three years—large in scale and highly complex—gaining rich knowledge and practice. Systematically summarizing and sharing experiences is not only a reflection on growth, but also a way to organize myself at this stage.
5. In some sense, this can also be a special handover and project knowledge inheritance. (Not really, haha 😆)
6. This series is also my final contribution to the team and platform. Although I have some criticisms of the daily working mechanisms, I must admit that this experience brought growth and the expected rewards—it was all worthwhile.
7. Since 2024, after completing major upgrades to the spreadsheet system, the document system has gradually entered a maintenance phase. I have long wanted to write such a review and summary; now I finally have the chance.
8. I once created a Dream Project list, which is still growing. I am fortunate to have deeply participated in the development of this system for three years and witnessed its increasing complexity. Every time I talk with colleagues, I feel extremely lucky—nowadays, almost no one knows this system better than I do. [[Challenging projects every programmer should try]]

### Specifications

This series has the following requirements and positioning:

- All images use English interfaces, ensuring the entire image content is in English.
- Articles are bilingual (Chinese and English), with careful control over the English expression and quality.
- Focus is on overall structure and core ideas, aiming to explain clearly and understandably for readers without professional backgrounds, thus omitting overly detailed parameters and minutiae.
- Meanwhile, considering the needs of some project team members, the content can also serve as a technical index and quick reference.
- No company or platform-specific terms will appear; the content strives for neutrality and clarity.
- Content covers key principles and implementation ideas of both frontend and backend, not limited to a specific layer, and illustrated with real cases.
- Rejects “corporate flavor” full of useless flowcharts and internal jargon, no hollow boast; emphasizes geek spirit and technical practice, truly presenting the essence of problems and their solutions. It should be clear that “hundreds of millions of users” is a platform capability, not a programmer’s personal glory.
- Introduces not only current implementations but also possible future ideas and solutions to broaden readers’ horizons.
- Business details are not overly elaborated (e.g., template libraries, document covers, bidirectional links are not specially described).
- Technical content is moderate, so non-experts in this field can understand system architecture and core challenges, while technical professionals can gain practical ideas and insights about complex problems.
- Every article begins with a clear table of contents for easier navigation.

## Project and Me

![](https://cdn.jsdelivr.net/gh/highestpeak/public-image@master//20250704130448048.png "My First Day of Work, on July 8, 2021.")

As stated in my [cv](/about/curriculum-vitae), since joining in 2021, over two and a half years, I worked on several modules: from content violation review, document search, to stateless architecture upgrades of the online spreadsheet, read/write performance optimization, and iterations of collaboration algorithms and protocols. After the team and company shifted to AI strategy, I also explored the integration of document systems with large language models (LLMs)—including how to efficiently vectorize massive amounts of documents and spreadsheets, and how to organize segmented text fragments. The trade-offs and strategies have been much more numerous and complex than initially expected.

Among all projects, the online spreadsheet system is where I have focused the longest and put in the most effort. I devoted a full two years to this system—such a long time. I once wrote on my Dream Project list “build a spreadsheet system,” and now I not only achieved it but also added collaboration algorithm upgrades 😃. So this series will try to provide a detailed description of the online spreadsheet system, helping everyone understand such a large-scale and complex system. Even if you don’t engage with similar systems directly, seeing how data structures and algorithms are applied and iterated in real systems is surely beneficial.

Although my main focus is the online spreadsheet, I also have considerable knowledge of online document implementations due to the business connections. In fact, although online documents have much more data and users than spreadsheets (about a 3:1 ratio), from data structure and business complexity perspectives, spreadsheets are actually more challenging. Documents, as a more general and common product type, make collaboration algorithms easier to understand. Therefore, for completeness and readability, I will start with detailed discussion of online documents and then extend to the more challenging online spreadsheet.

Besides documents and spreadsheets, a complete web product, especially enterprise-level web systems, usually includes a set of foundational supporting modules which I call meta systems. These include permission management, user management, file organization, search platforms, and admin dashboards. Although briefly described, these modules are highly generic and indispensable for nearly all web systems—whether large businesses or small independent products—often relying on similar SaaS services like Algolia. They are thus very worthy of exploration.

Finally, before the content begins, I must thank the teammates who have guided and supported me along the way. I thank my mentor Qiang, who taught me a lot—not only in deep professional skills but also in ways of doing things and thinking, truly a teacher and friend. I also thank my project colleagues, who helped me greatly in those early years, significantly broadening my technical vision and capabilities. I’m very grateful to my team leader and manager as well. Honestly, being able to work on this project is a kind of luck—such projects are rare. Even joining the team, the timing and environment must be just right to reap full benefits. After years of cooperation, my colleagues have become good friends—something not so common in China’s workplace environment.

![](https://cdn.jsdelivr.net/gh/highestpeak/public-image@master//0411F0BF-1BA3-478A-BAD3-E20C855B2CD9_1_201_a.jpeg "My Last Day of Work, on February 10, 2025.")
## Blueprint

The envisaged table of contents is as follows:

- Part 1 Introduction
  - [[0-Introduction To Collaboration Document System]]
- Part 2 Document
  - [[1-0-Document]]
  - [[1-1-Document-Snapshot]]
- Part 3 Online Spreadsheet
  - [[2-0-Introduction To Online Spreadsheet]]
  - [[2-1-Model Of Online Spreadsheet]]
  - [[2-2-Collaboration Architecture Of Online Spreadsheet]]
  - [[2-3-Protocol Of Online Spreadsheet]]
  - [[2-4-Services Of Online Spreadsheet]]
  - Typical Complex Business Details
  - [[2-5-Complexity-Formula]]
  - [[2-5-Complexity-Pivot Table]]
  - [[2-5-Complexity-Big Table]]
  - [[2-5-Complexity-UI-Rendering]]
  - [[2-5-Complexity-AI With Spreadsheet]]
  - [[2-5-Complexity-Data Structure]]
- Part 4 Data Structures and Collaboration Algorithms for Multiple Document Types, Version History
  - [[3-1-Data Structure Of Other Document Types]]
  - [[3-2-History Implement]]
  - (Collaboration algorithms will be omitted unless notably different)
- Part 5 Other Related Modules
  - [[4-1-Data Structure Of Other Modules]]
  - [[4-2-Algorithm Of Other Modules]]
  - [[4-3-Database Usage]]
  - [[4-4-Architecture Of Docs]]
  - [[4-6-Permission Module]]
  - [[4-7-Search Module]]
