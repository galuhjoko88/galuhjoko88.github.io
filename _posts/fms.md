---
title: FMS Mining and analogy
date: 2023-10-15 01:00:00 +0700
categories: [fms]
tags: [mining fms, commong fms,iot]
---


## Modern Mining Fleet Management System

In contemporary times, numerous companies employ the term Fleet Management System (FMS). My expertise lies specifically in the domain of Mining FMS, which, as I comprehend, amalgamates conventional FMS in IoT transportation with IoT in manufacturing. Drawing an intuitive analogy, in the realm of mining, transportation serves as a pivotal tool for production creation. This stands in contrast to transportation in manufacturing, where it is typically regarded as an essential yet non-value-added element in the process. This nuanced perspective emphasizes the unique dynamics and critical role that transportation plays in the mining sector, diverging from its role in traditional manufacturing practices.


![FMS comparation](</assets/img/fms/comparation fms and iot.PNG>)


Fleet Management Systems (FMS), a widely recognized term, typically involve the movement of transportation units along expansive and multi-route road paths. This can include transportation between warehouses and factories, warehouses and retailers, sub-warehouses and retailers, encompassing the last mile. Such transportation occurs in various locations, be it in cities, towns, villages, or any open path worldwide. The FMS operates as an [open queue network](https://homepages.inf.ed.ac.uk/jeh/Simjava/queueing/Networks/networks.html), where the movement of each transportation unit remains unaffected by others. The commonly utilized technology is commercial broadband, chosen for its availability, and 4G is currently the preferred option, given its acceptable latency requirements. The primary functions of this system include tracking with minimal intervention from the command center.

In the context of IoT in manufacturing facilities, its application heavily depends on the involved processes, dictating the type of machinery used. IoT is typically attached or embedded onto machinery, delivering data (messaging data) at regular intervals. Manufacturing facilities are often enclosed within buildings. Low latency is crucial for certain processes requiring quick decisions, such as an automatic QC inspector sending alerts when specification limits are breached. Some sensors may not require such low latency, such as an oven temperature sensor in car painting, which might only need periodic checks every 30 minutes, resulting in less dense data. Manufacturing IoT primarily serves preventive maintenance, visual inspection, or other simple AI-related processes. Several factories have already adopted this technology, and data communication in such setups typically uses commercial broadband (M2M) or private networks with lower costs.

In the mining industry, FMS combines IoT in manufacturing and commonly known FMS. IoT devices are embedded in production units that traverse vast areas. Transportation in mining occurs within a [closed queue network](https://homepages.inf.ed.ac.uk/jeh/Simjava/queueing/Networks/networks.html), meaning disruptions in transportation flow impact subsequent processes. This necessitates well-managed short-term decision-making for transportation and command center function become critical in the industry, requiring very low latency due to the critical nature of each unit transporter's movement. Remote mining locations may opt for private network setups to manage latency, while others relying solely on tracking may use common broadband with limited availability. Unlike other industries, transportation costs in the mining industry can rank among the top 3 or 4 in the P&L statement, underscoring its significance. Efficiently managing units with FMS in the short term directly influences the company's total transportation costs. Proper utilization of FMS can lead to significant cost savings, while mismanagement can result in wasted investment, however the company will be known as an adopter of "Fleet Management Systems."
