---
title: App Azure SaaS SQL con indicizzazione dei video | Microsoft Docs
description: "Questo articolo indicizza varie posizioni temporali nel nostro video di 81 minuti sulla progettazione dell'app di tenancy DB SaaS, dalla conferenza Ignite che si è tenuta l'11 ottobre 2017. È possibile passare direttamente alla parte di interesse. Vengono descritti almeno 3 criteri. Vengono descritte le funzionalità di Azure che semplificano lo sviluppo e la gestione."
ms.custom: 
ms.date: 12/06/2017
ms.prod: sql-server
ms.reviewer: billgib
ms.suite: 
ms.technology: database-engine
ms.tgt_pltfrm: 
ms.topic: article
author: MightyPen
ms.author: genemi
manager: craigg
ms.workload: Inactive
ms.openlocfilehash: db47d1dd0a6114ebca0715da179cc0629b26781a
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2017
---
# <a name="video-indexed-and-annotated-for-mulit-tenant-saas-app-using-azure-sql-database"></a>Annotazioni e indicizzazione dei video per l'app SaaS multi-tenant con il database SQL di Azure

Questo articolo è un indice con annotazioni nelle posizioni temporali di un video di 81 minuti sui criteri o sui modelli di tenancy SaaS. L'articolo consente di spostarsi avanti o indietro nel video fino alla parte di interesse. Il video illustra le principali opzioni di progettazione per un'applicazione di database multi-tenant nel database SQL di Azure. Il video include demo, procedure dettagliate di codice di gestione e, talvolta, dettagli ricavati dalle esperienze più approfonditi rispetto a quanto disponibile nella documentazione.

Il video analizza maggiormente in dettaglio le informazioni disponibili nella nostra documentazione disponibile in: 
- *Informazioni generali:* [Criteri di tenancy di database delle applicazioni SaaS multi-tenant][saas-concept-design-patterns-563e]
- *Esercitazioni:* [Applicazione SaaS Wingtip Tickets][saas-how-welcome-wingtip-app-679t]

Il video e gli articoli descrivono le varie fasi di creazione di un'applicazione multi-tenant nel database SQL di Azure nel cloud. Speciali funzionalità del database SQL di Azure agevolano lo sviluppo e l'implementazione di app multi-tenant più semplici da gestire e affidabili in termini di prestazioni.

La nostra documentazione scritta viene periodicamente aggiornata. Il video non viene modificato né aggiornato, pertanto gran parte delle informazioni potrebbero risultare in futuro obsolete.



## <a name="sequence-of-38-time-indexed-screenshots"></a>Sequenza di 38 screenshot con indicizzazione temporale

Questa sezione indicizza la posizione temporale per 38 discussioni all'interno del video di 81 minuti. Ogni indice temporale include annotazioni con uno screenshot del video e, talvolta, informazioni aggiuntive.

Ogni indice temporale è indicato nel formato *h:mm:ss*. Ad esempio, la seconda posizione temporale indicizzata, etichettata **Obiettivi della sessione**, inizia più o meno alla posizione temporale **0:03:11**.


### <a name="compact-links-to-video-indexed-time-locations"></a>Collegamenti compatti a posizioni temporali con indicizzazione del video

I titoli seguenti sono collegamenti alle corrispondenti sezioni con annotazioni riportate più avanti in questo articolo:

- [1. **(Inizio)** Diapositiva di benvenuto, 0:00:03](#anchor-image-wtip-min00001)
- [2. Obiettivi della sessione, 0:03:11](#anchor-image-wtip-min00311)
- [3. Agenda, 0:04:17](#anchor-image-wtip-min00417)
- [4. App Web multi-tenant, 0:05:05](#anchor-image-wtip-min00505)
- [5. Modulo Web dell'app in azione, 0:05:55](#anchor-image-wtip-min00555)
- [6. Costo per tenant (scalabilità, isolamento, recupero), 0:09:31](#anchor-image-wtip-min00931)
- [7. Modelli di database per multi-tenant: vantaggi e svantaggi, 0:11:59](#anchor-image-wtip-min01159)
- [8. Il modello ibrido unisce i vantaggi del multi-tenant e del tenant singolo, 0:13:01](#anchor-image-wtip-min01301)
- [9. Tenant singolo e multi-tenant: vantaggi e svantaggi, 0:16:44](#anchor-image-wtip-min01644)
- [10. I pool sono convenienti per carichi di lavoro imprevedibili, 0:19:36](#anchor-image-wtip-min01936)
- [11. Demo di modelli di database per tenant e ibridi a tenant singolo/multi-tenant, 0:20:08](#anchor-image-wtip-min02008)
- [12. Modulo dell'app attiva che illustra Dojo, 0:20:29](#anchor-image-wtip-min02029)
- [13. MYOB e non un DBA in vista, 0:28:54](#anchor-image-wtip-min02854)
- [14. Esempio di utilizzo del pool elastico MYOB, 0:29:40](#anchor-image-wtip-min02940)
- [15. Apprendimento dal modello MYOB e da altri fornitori di software indipendenti, 0:31:36](#anchor-image-wtip-min03136)
- [16. Criteri di composizione in uno scenario SaaS E2E, 0:43:15](#anchor-image-wtip-min04315)
- [17. App SaaS multi-tenant ibrida canonica, 0:47:33](#anchor-image-wtip-min04733)
- [18. App di esempio SaaS Wingtip, 0:48:10](#anchor-image-wtip-min04810)
- [19. Scenari e criteri illustrati nelle esercitazioni, 0:49:10](#anchor-image-wtip-min04910)
- [20. Demo di esercitazioni e repository Github, 0:50:18](#anchor-image-wtip-min05018)
- [21. Repository Github Microsoft/Wingtip SaaS, 0:50:38](#anchor-image-wtip-min05038)
- [22. Esplorazione dei criteri, 0:56:20](#anchor-image-wtip-min05620)
- [23. Provisioning di tenant e onboarding, 0:57:44](#anchor-image-wtip-min05744)
- [24. Provisioning di tenant e connessione dell'applicazione, 0:58:58](#anchor-image-wtip-min05858)
- [25. Demo di script di gestione per il provisioning di un singolo tenant, 0:59:43](#anchor-image-wtip-min05943)
- [26. PowerShell per il provisioning e il catalogo, 1:00:02](#anchor-image-wtip-min10002)
- [27. T-SQL SELECT * FROM TenantsExtended, 1:03:30](#anchor-image-wtip-min10330)
- [28. Gestione di carichi di lavoro tenant imprevedibili, 1:04:36](#anchor-image-wtip-min10436)
- [29. Monitoraggio di pool elastici, 1:06:39](#anchor-image-wtip-min10639)
- [30. Monitoraggio di prestazioni e generazione del carico, 1:09:42](#anchor-image-wtip-min10942)
- [31. Gestione di schemi su larga scala, 1:10:33](#anchor-image-wtip-min11033)
- [32. Query distribuite tra database tenant, 1:12:21](#anchor-image-wtip-min11221)
- [33. Demo di generazione di ticket, 1:12:32](#anchor-image-wtip-min11232)
- [34. Analisi ad hoc SSMS, 1:12:46](#anchor-image-wtip-min11246)
- [35. Estrarre i dati del tenant in SQL Data Warehouse, 1:16:32](#anchor-image-wtip-min11632)
- [36. Grafico della distribuzione di vendita giornaliera, 1:16:48](#anchor-image-wtip-min11648)
- [37. Riepilogo e invito all'azione, 1:19:52](#anchor-image-wtip-min11952)
- [38. Risorse per altre informazioni, 1:20:42](#anchor-image-wtip-min12042)


&nbsp;

### <a name="annotated-index-time-locations-in-the-video"></a>Posizioni temporali di indice con annotazioni nel video

Fare clic su uno screenshot per passare alla posizione temporale esatta nel video.


&nbsp;<a name="anchor-image-wtip-min00001"/>
#### <a name="1-start-welcome-slide-00001"></a>1. *(Inizio)* Diapositiva di benvenuto, 0:00:01

*Apprendimento dal modello MYOB: criteri di progettazione per applicazioni SaaS in un database SQL di Azure - BRK3120*

[![Diapositiva di benvenuto][image-wtip-min00003-brk3120-whole-welcome]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1)

- Titolo: Apprendimento dal modello MYOB: criteri di progettazione per applicazioni SaaS in un database SQL di Azure
- Bill.Gibson@microsoft.com
- Principal Program Manager, database SQL di Azure
- Sessione di Microsoft Ignite BRK3120, Orlando, FL USA, 11 ottobre 2017


&nbsp;<a name="anchor-image-wtip-min00311"/>
#### <a name="2-session-objectives-00153"></a>2. Obiettivi della sessione, 0:01:53
[![Obiettivi della sessione][image-wtip-min00311-session]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=113)

- Modelli alternativi per app multi-tenant, con vantaggi e svantaggi.
- Criteri SaaS per ridurre i costi di sviluppo, gestione e risorse.
- App di esempio + script.
- Funzionalità PaaS + criteri SaaS rendono il database SQL una piattaforma dati altamente scalabile e conveniente per SaaS multi-tenant.


&nbsp;<a name="anchor-image-wtip-min00417"/>
#### <a name="3-agenda-00409"></a>3. Agenda, 0:04:09
[![Agenda][image-wtip-min00417-agenda]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=249)


&nbsp;<a name="anchor-image-wtip-min00505"/>
#### <a name="4-multi-tenant-web-app-00500"></a>4. App Web multi-tenant, 0:05:00
[![App SaaS Wingtip: app Web multi-tenant][image-wtip-min00505-web-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=300)


&nbsp;<a name="anchor-image-wtip-min00555"/>
#### <a name="5-app-web-form-in-action-00539"></a>5. Modulo Web dell'app in azione, 0:05:39
[![Modulo Web dell'app in azione][image-wtip-min00555-app-web-form]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=339)


&nbsp;<a name="anchor-image-wtip-min00931"/>
#### <a name="6-per-tenant-cost-scale-isolation-recovery-00658"></a>6. Costo per tenant (scalabilità, isolamento, recupero), 0:06:58
[![Costo per tenant (scalabilità, isolamento, recupero)][image-wtip-min00931-per-tenant-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=418)


&nbsp;<a name="anchor-image-wtip-min01159"/>
#### <a name="7-database-models-for-multi-tenant-pros-and-cons-00952"></a>7. Modelli di database per multi-tenant: vantaggi e svantaggi, 0:09:52
[![Modelli di database per multi-tenant: vantaggi e svantaggi][image-wtip-min01159-db-models-pros-cons]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=592)


&nbsp;<a name="anchor-image-wtip-min01301"/>
#### <a name="8-hybrid-model-blends-benefits-of-mtst-01229"></a>8. Il modello ibrido unisce i vantaggi del multi-tenant e del tenant singolo, 0:12:29
[![Il modello ibrido unisce i vantaggi del multi-tenant e del tenant singolo][image-wtip-min01301-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=749)


&nbsp;<a name="anchor-image-wtip-min01644"/>
#### <a name="9-single-tenant-vs-multi-tenant-pros-and-cons-01311"></a>9. Tenant singolo e multi-tenant: vantaggi e svantaggi, 0:13:11
[![Tenant singolo e multi-tenant: vantaggi e svantaggi][image-wtip-min01644-st-vs-mt]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=791)


&nbsp;<a name="anchor-image-wtip-min01936"/>
#### <a name="10-pools-are-cost-effective-for-unpredictable-workloads-01749"></a>10. I pool sono convenienti per carichi di lavoro imprevedibili, 0:17:49
[![I pool sono convenienti per carichi di lavoro imprevedibili][image-wtip-min01936-pools-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1069)


&nbsp;<a name="anchor-image-wtip-min02008"/>
#### <a name="11-demo-of-database-per-tenant-and-hybrid-stmt-01959"></a>11. Demo di modelli di database per tenant e ibridi a tenant singolo/multi-tenant, 0:19:59
[![Demo di modelli di database per tenant e ibridi a tenant singolo/multi-tenant][image-wtip-min02008-demo-st-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1199)


&nbsp;<a name="anchor-image-wtip-min02029"/>
#### <a name="12-live-app-form-showing-dojo-02010"></a>12. Modulo dell'app attiva che illustra Dojo, 0:20:10
[![Modulo dell'app attiva che illustra Dojo][image-wtip-min02029-live-app-form-dojo]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1210)

&nbsp;<a name="anchor-image-wtip-min02854"/>
#### <a name="13-myob-and-not-a-dba-in-sight-02506"></a>13. MYOB e non un DBA in vista, 0:25:06
[![MYOB e non un DBA in vista][image-wtip-min02854-myob-no-dba]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1506)


&nbsp;<a name="anchor-image-wtip-min02940"/>
#### <a name="14-myob-elastic-pool-usage-example-02930"></a>14. Esempio di utilizzo del pool elastico MYOB, 0:29:30
[![Esempio di utilizzo del pool elastico MYOB][image-wtip-min02940-myob-elastic]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1770)


&nbsp;<a name="anchor-image-wtip-min03136"/>
#### <a name="15-learning-from-myob-and-other-isvs-03125"></a>15. Apprendimento dal modello MYOB e da altri fornitori di software indipendenti, 0:31:25
[![Apprendimento dal modello MYOB e da altri fornitori di software indipendenti][image-wtip-min03136-learning-isvs]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1885)


&nbsp;<a name="anchor-image-wtip-min04315"/>
#### <a name="16-patterns-compose-into-e2e-saas-scenario-03142"></a>16. Criteri di composizione in uno scenario SaaS E2E, 0:31:42
[![Criteri di composizione in uno scenario SaaS E2E][image-wtip-min04315-patterns-compose]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1902)


&nbsp;<a name="anchor-image-wtip-min04733"/>
#### <a name="17-canonical-hybrid-multi-tenant-saas-app-04604"></a>17. App SaaS multi-tenant ibrida canonica, 0:46:04
[![App SaaS multi-tenant ibrida canonica][image-wtip-min04733-canonical-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2764)


&nbsp;<a name="anchor-image-wtip-min04810"/>
#### <a name="18-wingtip-saas-sample-app-04801"></a>18. App di esempio SaaS Wingtip, 0:48:01
[![App di esempio SaaS Wingtip][image-wtip-min04810-wingtip-saas-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2881)


&nbsp;<a name="anchor-image-wtip-min04910"/>
#### <a name="19-scenarios-and-patterns-explored-in-the-tutorials-04900"></a>19. Scenari e criteri illustrati nelle esercitazioni, 0:49:00
[![Scenari e criteri illustrati nelle esercitazioni][image-wtip-min04910-scenarios-tutorials]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2940)


&nbsp;<a name="anchor-image-wtip-min05018"/>
#### <a name="20-demo-of-tutorials-and-github-repository-05012"></a>20. Demo di esercitazioni e repository Github, 0:50:12
[![Demo di esercitazioni e repository Github][image-wtip-min05018-demo-tutorials-github]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3012)


&nbsp;<a name="anchor-image-wtip-min05038"/>
#### <a name="21-github-repo-microsoftwingtipsaas-05032"></a>21. Repository Github Microsoft/Wingtip SaaS, 0:50:32
[![Repository Github Microsoft/WingtipSaaS][image-wtip-min05038-github-wingtipsaas]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3032)


&nbsp;<a name="anchor-image-wtip-min05620"/>
#### <a name="22-exploring-the-patterns-05615"></a>22. Esplorazione dei criteri, 0:56:15
[![Esplorazione dei criteri][image-wtip-min05620-exploring-patterns]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3375)


&nbsp;<a name="anchor-image-wtip-min05744"/>
#### <a name="23-provisioning-tenants-and-onboarding-05619"></a>23. Provisioning di tenant e onboarding, 0:56:19
[![Provisioning di tenant e onboarding][image-wtip-min05744-provisioning-tenants-onboarding-1]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3379)


&nbsp;<a name="anchor-image-wtip-min05858"/>
#### <a name="24-provisioning-tenants-and-application-connection-05752"></a>24. Provisioning di tenant e connessione dell'applicazione, 0:57:52
[![Provisioning di tenant e connessione dell'applicazione][image-wtip-min05858-provisioning-tenants-app-connection-2]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3472)


&nbsp;<a name="anchor-image-wtip-min05943"/>
#### <a name="25-demo-of-management-scripts-provisioning-a-single-tenant-05936"></a>25. Demo di script di gestione per il provisioning di un singolo tenant, 0:59:36
[![Demo di script di gestione per il provisioning di un singolo tenant][image-wtip-min05943-demo-management-scripts-st]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3576)


&nbsp;<a name="anchor-image-wtip-min10002"/>
#### <a name="26-powershell-to-provision-and-catalog-05956"></a>26. PowerShell per il provisioning e il catalogo, 0:59:56
[![PowerShell per il provisioning e il catalogo][image-wtip-min10002-powershell-provision-catalog]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3596)


&nbsp;<a name="anchor-image-wtip-min10330"/>
#### <a name="27-t-sql-select--from-tenantsextended-10325"></a>27. T-SQL SELECT * FROM TenantsExtended, 1:03:25
[![T-SQL SELECT * FROM TenantsExtended][image-wtip-min10330-sql-select-tenantsextended]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3805)


&nbsp;<a name="anchor-image-wtip-min10436"/>
#### <a name="28-managing-unpredictable-tenant-workloads-10334"></a>28. Gestione di carichi di lavoro tenant imprevedibili, 1:03:34
[![Gestione di carichi di lavoro tenant imprevedibili][image-wtip-min10436-managing-unpredictable-workloads]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3814)


&nbsp;<a name="anchor-image-wtip-min10639"/>
#### <a name="29-elastic-pool-monitoring-10632"></a>29. Monitoraggio di pool elastici, 1:06:32
[![Monitoraggio di pool elastici][image-wtip-min10639-elastic-pool-monitoring]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3992)


&nbsp;<a name="anchor-image-wtip-min10942"/>
#### <a name="30-load-generation-and-performance-monitoring-10937"></a>30. Monitoraggio di prestazioni e generazione del carico, 1:09:37
[![Monitoraggio di prestazioni e generazione del carico][image-wtip-min10942-load-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4117)


&nbsp;<a name="anchor-image-wtip-min11033"/>
#### <a name="31-schema-management-at-scale-10940"></a>31. Gestione di schemi su larga scala, 1:09:40
[![Gestione di schemi su larga scala][image-wtip-min11033-schema-management-scale]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=34120)


&nbsp;<a name="anchor-image-wtip-min11221"/>
#### <a name="32-distributed-query-across-tenant-databases-11118"></a>32. Query distribuite tra database tenant, 1:11:18
[![Query distribuite tra database tenant][image-wtip-min11221-distributed-query]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4278)


&nbsp;<a name="anchor-image-wtip-min11232"/>
#### <a name="33-demo-of-ticket-generation-11228"></a>33. Demo di generazione di ticket, 1:12:28
[![Demo di generazione di ticket][image-wtip-min11232-demo-ticket-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4348)


&nbsp;<a name="anchor-image-wtip-min11246"/>
#### <a name="34-ssms-adhoc-analytics-11235"></a>34. Analisi ad hoc SSMS, 1:12:35
[![Analisi ad hoc SSMS][image-wtip-min11246-ssms-adhoc-analytics]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4355)


&nbsp;<a name="anchor-image-wtip-min11632"/>
#### <a name="35-extract-tenant-data-into-sql-dw-11546"></a>35. Estrarre i dati del tenant in SQL Data Warehouse, 1:15:46
[![Estrarre i dati del tenant in SQL Data Warehouse][image-wtip-min11632-extract-tenant-data-sql-dw]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4546)


&nbsp;<a name="anchor-image-wtip-min11648"/>
#### <a name="36-graph-of-daily-sale-distribution-11638"></a>36. Grafico della distribuzione di vendita giornaliera, 1:16:38
[![Grafico della distribuzione di vendita giornaliera][image-wtip-min11648-graph-daily-sale-distribution]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4598)


&nbsp;<a name="anchor-image-wtip-min11952"/>
#### <a name="37-wrap-up-and-call-to-action-11743"></a>37. Riepilogo e invito all'azione, 1:17:43
[![Riepilogo e invito all'azione][image-wtip-min11952-wrap-up-call-action]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4663)


&nbsp;<a name="anchor-image-wtip-min12042"/>
#### <a name="38-resources-for-more-information-12035"></a>38. Risorse per altre informazioni, 1:20:35
[![Risorse per altre informazioni][image-wtip-min12042-resources-more-info]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4835)

- [Post del blog, 22 maggio 2017][resource-blog-saas-patterns-app-dev-sql-db-768h]

- *Informazioni generali:* [Criteri di tenancy di database delle applicazioni SaaS multi-tenant][saas-concept-design-patterns-563e]

- *Esercitazioni:* [Applicazione SaaS Wingtip Tickets][saas-how-welcome-wingtip-app-679t]

- Repository Github per esplorare l'applicazione tenancy SaaS Wingtip Ticket:
    - [Repository Github per - Modello di applicazione autonomo][github-wingtip-standaloneapp].
    - [Repository Github per - Modello di database per tenant][github-wingtip-dbpertenant].
    - [Repository Github per - Modello di database multi-tenant][github-wingtip-multitenantdb].





## <a name="next-steps"></a>Passaggi successivi

- [Articolo della prima esercitazione][saas-how-welcome-wingtip-app-679t]




<!-- Image link reference IDs. -->

[image-wtip-min00003-brk3120-whole-welcome]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00003-brk3120-welcome-myob-design-saas-app-sql-db.png "Diapositiva di benvenuto"

[image-wtip-min00311-session]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00311-session-objectives-takeaway.png "Obiettivi della sessione."

[image-wtip-min00417-agenda]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00417-agenda-app-management-models-patterns.png "Agenda."

[image-wtip-min00505-web-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00505-wingtip-saas-app-mt-web.png "App SaaS Wingtip: app Web multi-tenant"

[image-wtip-min00555-app-web-form]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00555-app-form-contoso-concert-hall-night-opera.png "Modulo Web dell'app in azione"

[image-wtip-min00931-per-tenant-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00931-saas-data-management-concerns.png "Costo per tenant (scalabilità, isolamento, recupero)"

[image-wtip-min01159-db-models-pros-cons]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01159-db-models-multi-tenant-saas-apps.png "Modelli di database per multi-tenant: vantaggi e svantaggi"

[image-wtip-min01301-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01301-hybrib-model-blends-benefits-mt-st.png "Il modello ibrido unisce i vantaggi del multi-tenant e del tenant singolo"

[image-wtip-min01644-st-vs-mt]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01644-st-mt-pros-cons.png "Tenant singolo e multi-tenant: vantaggi e svantaggi"

[image-wtip-min01936-pools-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01936-pools-cost-effective-unpredictable-workloads.png "I pool sono convenienti per carichi di lavoro imprevedibili"

[image-wtip-min02008-demo-st-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02008-demo-st-hybrid.png "Demo di modelli di database per tenant e ibridi a tenant singolo/multi-tenant"

[image-wtip-min02029-live-app-form-dojo]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02029-app-form-dogwwod-dojo.png "Modulo dell'app attiva che illustra Dojo"

[image-wtip-min02854-myob-no-dba]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02854-myob-no-dba.png "MYOB e non un DBA in vista"

[image-wtip-min02940-myob-elastic]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02940-myob-elastic-pool-usage-example.png "Esempio di utilizzo del pool elastico MYOB"

[image-wtip-min03136-learning-isvs]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min03136-myob-isv-saas-patterns-design-scale.png "Apprendimento dal modello MYOB e da altri fornitori di software indipendenti"

[image-wtip-min04315-patterns-compose]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04315-patterns-compose-into-e2e-saas-scenario-st-mt.png "Criteri di composizione in uno scenario SaaS E2E"

[image-wtip-min04733-canonical-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04733-canonical-hybrid-mt-saas-app.png "App SaaS multi-tenant ibrida canonica"

[image-wtip-min04810-wingtip-saas-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04810-saas-sample-app-descr-of-modules-links.png "App di esempio SaaS Wingtip"

[image-wtip-min04910-scenarios-tutorials]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04910-scenarios-patterns-explored-tutorials.png "Scenari e criteri illustrati nelle esercitazioni"

[image-wtip-min05018-demo-tutorials-github]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05018-demo-saas-tutorials-github-repo.png "Demo di esercitazioni e repository Github"

[image-wtip-min05038-github-wingtipsaas]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05038-github-repo-wingtipsaas.png "Repository Github Microsoft/Wingtip SaaS"

[image-wtip-min05620-exploring-patterns]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05620-exploring-patterns-tutorials.png "Esplorazione dei criteri"

[image-wtip-min05744-provisioning-tenants-onboarding-1]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05744-provisioning-tenants-connecting-run-time-1.png "Provisioning di tenant e onboarding"

[image-wtip-min05858-provisioning-tenants-app-connection-2]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05858-provisioning-tenants-connecting-run-time-2.png "Provisioning di tenant e connessione dell'applicazione"

[image-wtip-min05943-demo-management-scripts-st]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05943-demo-management-scripts-provisioning-st.png "Demo di script di gestione per il provisioning di un singolo tenant"

[image-wtip-min10002-powershell-provision-catalog]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10002-powershell-code.png "PowerShell per il provisioning e il catalogo"

[image-wtip-min10330-sql-select-tenantsextended]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10330-ssms-tenantcatalog.png "T-SQL SELECT * FROM TenantsExtended"

[image-wtip-min10436-managing-unpredictable-workloads]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10436-managing-unpredictable-tenant-workloads.png "Gestione di carichi di lavoro tenant imprevedibili"

[image-wtip-min10639-elastic-pool-monitoring]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10639-elastic-pool-monitoring.png "Monitoraggio di pool elastici"

[image-wtip-min10942-load-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10942-schema-management-scale.png "Monitoraggio di prestazioni e generazione del carico"

[image-wtip-min11033-schema-management-scale]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11033-schema-manage-1000s-dbs-one.png "Gestione di schemi su larga scala"

[image-wtip-min11221-distributed-query]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11221-distributed-query-all-tenants-asif-single-db.png "Query distribuite tra database tenant"

[image-wtip-min11232-demo-ticket-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11232-demo-ticket-generation-distributed-query.png "Demo di generazione di ticket"

[image-wtip-min11246-ssms-adhoc-analytics]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11246-tsql-adhoc-analystics-db-elastic-query.png "Analisi ad hoc SSMS"

[image-wtip-min11632-extract-tenant-data-sql-dw]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11632-extract-tenant-data-analytics-db-dw.png "Estrarre i dati del tenant in SQL Data Warehouse"

[image-wtip-min11648-graph-daily-sale-distribution]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11648-graph-daily-sale-contoso-concert-hall.png "Grafico della distribuzione di vendita giornaliera"

[image-wtip-min11952-wrap-up-call-action]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11952-wrap-call-action-saasfeedback.png "Riepilogo e invito all'azione"

[image-wtip-min12042-resources-more-info]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min12042-resources-blog-github-tutorials-get-started.png "Risorse per altre informazioni"




<!-- Article link reference IDs. -->

[saas-concept-design-patterns-563e]: saas-tenancy-app-design-patterns.md

[saas-how-welcome-wingtip-app-679t]: saas-tenancy-welcome-wingtip-tickets-app.md


[video-on-youtube-com-478y]: https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1

[video-on-channel9-479c]: https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3120


[resource-blog-saas-patterns-app-dev-sql-db-768h]: https://azure.microsoft.com/blog/saas-patterns-accelerate-saas-application-development-on-sql-database/


[github-wingtip-standaloneapp]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp/

[github-wingtip-dbpertenant]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/

[github-wingtip-multitenantdb]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/

