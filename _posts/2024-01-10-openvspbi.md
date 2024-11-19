---
title: Balancing Open Source Cost and Usability 
date: 2024-01-01 01:00:00 +0700
categories: [open source, power bi, dashboard]
tags: [power bi, dashboard, open source, business intelligence, self-service analytics, analytics]
---
 
# Balancing Cost and Usability Lessons from a Dashboard Development Journey to achieve self-service Analytics

## **Disclaimer**:
**This comparison is not intended as an advertisement for Microsoft or any of its products.** The choice of Power BI as a reference point in this discussion is coincidental and based solely on its alignment with the needs of certain process owners. The suitability of any tool varies widely depending on specific use cases, organizational requirements, and resource availability. As such, this comparison may not hold for every condition and is meant to provide general guidance.  

#### **This is actual comparation between development of a dashboard using open source vs Power BI**
![comparationpbi](</assets/img/jetvspbi/opensource_pbi.png>)

Many people assume that open-source solutions are more cost-effective, leading many to overlook potential risks related to security or the complexities of system maintenance. However, in this article, I aim to challenge that assumption by demonstrating that this is not always the case. I will provide a real-world example where opting for open-source tools does not necessarily result in lower costs.

These two dashboards are quite similar in terms of content, but the difference in their user interfaces makes this less apparent. The open-source dashboard was the first one to be developed by external vendors, as the internal team lacked the SQL expertise required for data extraction and chart creation in the open-source platform. Surprisingly, even creating a simple chart demanded a significant SQL scripting knowledge. This complexity also explains why the development of this dashboard took as long as 20 working days—just for a single dashboard.

The Power BI dashboard was developed after the completion of the open-source dashboard, primarily due to concerns about the high costs associated with the latter. A fresh graduate, whom I personally trained using my experience in building data teams, took on the task of creating the Power BI dashboard. Despite having minimal Excel knowledge, he was eager to learn and embraced the challenge. Since the data sources were already accessible within the organization, setting up the data pipeline through Power BI's Datamart was straightforward. Initially, the department personnel were skeptical about using Power BI, perceiving it as a tool for tech experts. However, this fresh graduate demonstrated that it was possible to build a similar dashboard within just seven days, starting from zero knowledge. This repeat my success in developing a data team in previous company.  

In conclusion, here are the key takeaways:

1. Cost Perspective: Based on calculations, the total cost of Power BI would equal the vendor cost of the open-source dashboard in about 22 years. This assumes no modifications to the dashboard over time—a scenario that is highly improbable in a dynamic, heavily regulated, and project-based industry like mining.

2. Additional Costs: Interestingly, the open-source dashboard is hosted on a GCP server, which adds further costs depending on factors like egress volume, DevOps personnel, and other associated expenses. While these additional costs are not detailed here, they suggest that the break-even point mentioned in point 1 will be far more beyond 22 years.

3. Quality Perspective: The open-source dashboard offers a limited selection of color palettes and widgets, making it less user-friendly for regular business users, even though it may appeal to tech enthusiasts. Ultimately, the UI preference is subjective, and you can decide whether the open-source design aligns with your needs.
	

I am a strong advocate for open-source solutions when used effectively and in efficient manner. However, this case exemplifies inefficient use of open-source tools. There are scenarios where such a dashboard can truly excel, such as when full customization is required, the organization handles extremely large datasets, and the team possesses the technical expertise to match the tool's requirements. In non-technical companies, however, most employees are often unprepared or unwilling to invest the time needed to learn SQL, making open-source solutions less practical in such environments.

Back to our context, Power BI was chosen for this case primarily due to its cost-effectiveness and appearance, being significantly cheaper than alternatives like Tableau or Qlik. Additionally, it addressed the specific needs of this business department:

a. The desire to implement self-service analytics.

b. The organization already had a Microsoft ecosystem in place, ready for seamless integration.

c. A need for speed and efficiency—waiting 20 days for a single-page dashboard with limited visuals was absoultely not feasible, especially while employees were still developing their SQL skills.

d. Scalability requirements were minimal, as the project-based nature of the business meant data volumes were relatively small (a few hundred thousand records annually), unlike data-intensive industries such as e-commerce, telecomunication or other B2C/C2C industry.

e. The solution needed to remain within a specific budget to minimize administrative complexities.

### Conclusion 

This case highlights an important lesson: while open-source tools are often perceived as cost-effective, their suitability depends heavily on the specific context and organizational readiness. The comparison between the open-source and Power BI dashboards revealed that open-source solutions can incur unexpected costs, both in terms of development time and additional infrastructure requirements.

For this particular use case, Power BI emerged as the more practical and cost-efficient choice due to its lower initial and long-term costs, compatibility with the existing Microsoft ecosystem, and user-friendly features that catered to a non-technical team. It enabled faster development times and empowered employees to adopt self-service analytics without requiring extensive SQL knowledge.

However, it is essential to recognize that this does not imply open-source tools are inherently inefficient. They excel in scenarios where full customization, handling massive datasets, or leveraging a technically skilled team are critical requirements. Open-source solutions shine in environments where the necessary expertise and infrastructure are already in place.

Ultimately, the key takeaway is that the choice between open-source and proprietary tools should be guided by a clear understanding of the organization's needs, resources, and long-term goals. No single solution fits every situation, and decisions should be made based on what delivers the most value to the business.
