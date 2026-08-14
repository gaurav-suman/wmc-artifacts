# Answer the following questions in writing and upload to Github repo

## Q1. Is there any situation that describes an unconventional route taken in your experience?If so, please share a paragraph or two about this.

**Ans.** I faced one such situation while working as a software lead in the Wellington Alpine project. In one production performance issue, the application started facing multiple full GCs every few minutes, which was putting pressure on application stability.

I first added GC tracking logs, CloudWatch queries, and extra logs for load, request count, and cache behaviour to understand the pattern clearly. After tracking it for almost a week, we found that one design was storing position and instrument data in in-memory ConcurrentHashMaps, where column names and values were kept together. The maps kept growing because there was no eviction policy, no size limit, and no separation between repeated keys and changing values.

The unconventional route was what I did next: instead of waiting for one perfect solution on paper, I used UIT as a controlled experiment area and tried multiple small fixes through trial and error. I tested map size limits, LRU-based eviction, stale-entry cleanup, reduced unnecessary object creation, and cache refresh tuning. Some changes worked partially, some failed, but every deployment gave more clarity. Finally, I split the cache into two maps: one for common column names, which had only a few distinct entries, and another for changing values. This reduced duplicate memory usage significantly and brought GC behaviour under control. For me, the unconventional part was using disciplined trial and error in a safe environment to reach the right design fix.

## Q2. Do you recall a situation where you intensely and relentlessly focused to completa a task(can be professional work or hobby) that helped you grow?If so, please describe this experience in a paragraph or two.

**Ans.** At my previous company It works parcel delivery domain, we had to onboard a new logistics partner just before the Diwali sale season. The business team had already committed a launch date, so delaying it was not really an option. Initially, we thought it would be a normal integration, but once testing started, we found several issues. Some APIs behaved differently from the documentation, data formats were inconsistent, and new issues kept appearing during end-to-end testing.

As the team lead, I got deeply involved in the project. For almost three weeks, I worked closely with my team, business stakeholders, and the partner's technical team. Many times, we would fix one issue only to discover another. There were moments when it felt like we were going in circles, but we kept breaking problems into smaller pieces and solving them one by one. My focus during that period was to keep the team aligned, remove blockers quickly, and make sure progress continued every day.

We successfully launched the integration before the sale period, and it handled the expected shipment volumes without any major issues. This experience helped me grow because it taught me how to stay focused when there is a lot of uncertainty and pressure. I also learned that in such situations, leadership is not about having all the answers, but it is about keeping the team motivated and moving forward until the job is done.

## Q3. Describe an experience of joining new team, project or environment where there was significant amount of context, information or complexity to absorb in a relatively short period of time.

## How did you approach learning, building clarity, prioritizing what to focus on and gradually becoming effective?

**Ans.** One recent experience was when I joined the Wellington project, The finance domain was new to me. I had to quickly understand financial terms, system dependencies, data flows, policies, and a different work culture focused on compliance and approvals. Instead of trying to learn everything at once, I focused first on critical business flows, key applications, daily jobs, and areas where my contribution was immediately needed.

While learning the project, AI also came into the picture and I started using it in a practical way to improve my day-to-day productivity. I used Copilot for understanding and updating documents, reviewing pull requests, analysing issues, preparing RCA drafts, and converting scattered information into clear notes. I also explored creating small productivity tools using concepts like MCP and Skills, so repeated tasks could be handled in a more structured way. As someone with more than 10 years of software engineering experience, I did not see AI as a shortcut, but as a support system to learn faster, validate my understanding, and improve the quality of my work.

Gradually, I became comfortable with the domain and started contributing effectively in discussions, issue analysis, documentation, and delivery. This experience strengthened my belief that a good engineer must stay curious, respect the process, and adopt new tools like AI without losing core engineering judgement.
