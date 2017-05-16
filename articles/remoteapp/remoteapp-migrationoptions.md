---
title: Opzioni per la migrazione da Azure RemoteApp | Microsoft Docs
description: Informazioni sulle opzioni per la migrazione da Azure RemoteApp.
services: remoteapp
documentationcenter: 
author: ericorman
manager: mbaldwin
ms.assetid: c4e0e5bc-5c13-4487-b1b6-ebf2a5edc1f0
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: mbaldwin
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 11254a9bd82885701b457ba3b4ec0ef0aad710e6
ms.contentlocale: it-it
ms.lasthandoff: 04/27/2017


---
# <a name="options-for-migrating-out-of-azure-remoteapp"></a>Opzioni per la migrazione da Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp verrà sospeso a partire dal 31 agosto 2017. Per i dettagli, vedere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) .


Se si è interrotto l'uso di Azure RemoteApp dopo l'[annuncio del ritiro](https://go.microsoft.com/fwlink/?linkid=821148) o perché è terminato il periodo di valutazione, è necessario eseguire la migrazione da Azure RemoteApp a un altro servizio app. La migrazione può essere effettuata in due modi: con una distribuzione autogestita (spesso denominata Infrastructure as a Service [IaaS]) o tramite un'offerta completamente gestita (spesso denominata Platform as a Service oppure Software as a Service [PaaS/SaaS]). 

L'approccio IaaS self-service consiste in una distribuzione "fai da te" gestita dall'utente e di sua proprietà, direttamente distribuita su macchine virtuali (VM) o sistemi fisici. Altrimenti, un'offerta PaaS/SaaS completamente gestita è più simile ad Azure RemoteApp: un partner fornisce un livello di servizio in una soluzione .NET di comunicazione remota che gestisce gli aspetti operativi e di manutenzione, mentre il cliente si occupa di gestire app e immagini.

[Visualizzare il webinar di Azure RemoteApp sulle opzioni di migrazione](https://social.msdn.microsoft.com/Forums/azure/40557aaa-3e9f-403c-b221-ad3eac10dc56/migration-option-webinar-recordings?forum=AzureRemoteApp) oppure continuare a leggere per altre informazioni (inclusi alcuni esempi di diverse opzioni di hosting).

## <a name="self-managed-iaas-solutions"></a>Soluzioni autogestite (IaaS)
### <a name="rds-on-iaas"></a>**RDS su IaaS**
È possibile implementare una distribuzione di Servizi Desktop remoto (in Windows Server) basata su una sessione nativa tramite RemoteApp o desktop locali oppure in un ambiente ospitato (come le VM di Azure). Le distribuzioni di Servizi Desktop remoto su IaaS sono particolarmente indicate per i clienti che le conoscono già e che possiedono le necessarie competenze tecniche in merito. 

> [!NOTE]
> Per poter usufruire di questa opzione di distribuzione, è necessario disporre di un contratto multilicenza con Software Assurance (SA) per le licenze CAL per Servizi Desktop remoto.

Distribuire Servizi Desktop remoto nelle VM di Azure è più semplice che mai se si usano i modelli di distribuzione e applicazione delle patch (leggere una [panoramica](https://blogs.technet.microsoft.com/enterprisemobility/2015/07/13/azure-resource-manager-template-for-rds-deployment/) e quindi [accedere ai modelli](https://aka.ms/rdautomation)). Le stesse funzionalità di scalabilità elastica si ottengono con il modello di distribuzione classica di Azure (non il modello di risorse di Azure) all'interno di Azure RemoteApp tramite lo [script per la scalabilità automatica](https://gallery.technet.microsoft.com/scriptcenter/Automatic-Scaling-of-9b4f5e76), pur essendo disponibili altre personalizzazioni e configurazioni. Quando si distribuiscono i Servizi Desktop remoto su VM di Azure, è possibile avvalersi del [supporto tecnico di Azure](https://azure.microsoft.com/support/plans/), gestito dagli stessi professionisti che forniscono assistenza per Azure RemoteApp. Per una stima dei costi basata sull'uso esistente, è possibile contattare il [supporto tecnico di Azure](https://azure.microsoft.com/support/plans/) oppure eseguire il calcolo autonomamente usando l'apposito strumento presto disponibile.  Inoltre, con le VM serie N (attualmente in Private Preview), è possibile aggiungere vGPU. Per informazioni su come aggiungere vGPU e [sfruttare i miglioramenti dei Servizi Desktop remoto in Windows Server 2016](https://myignite.microsoft.com/videos/2794), è disponibile una sessione introduttiva.   

Per un aiuto in fase di distribuzione, è possibile consultare le guide dettagliate alla distribuzione di [Windows Server 2012 R2](http://aka.ms/rdsonazure) e [Windows Server 2016](http://aka.ms/rdsonazure2016). Per conoscere tutte le novità, consultare il [blog su Desktop remoto](https://blogs.technet.microsoft.com/enterprisemobility/?product=windows-server-remote-desktop-services).

### <a name="citrix-on-iaas"></a>**Citrix in IaaS**
Una distribuzione Citrix nativa di XenApp o XenDesktop in base alla sessione può essere effettuata in locale o in un ambiente ospitato (ad esempio, nelle VM di Azure). 

Per altre informazioni, consultare la guida dettagliata alla distribuzione [Citrix XA 7.6 in Azure](http://www.citrixandmicrosoft.com/Documents/Citrix-Azure Deployment Guide-v.1.0.docx). Sono inoltre disponibili altre informazioni su [Citrix in Azure](http://www.citrixandmicrosoft.com/Solutions/AzureCloud.aspx), con in più il calcolatore dei prezzi. Infine, è possibile discutere le opzioni disponibili con un [contatto Citrix](http://citrix.com/English/contact/index.asp).

## <a name="fully-managed-paassaas-offerings"></a>Offerte completamente gestite (PaaS/SaaS)

### <a name="awingu"></a>Awingu
Awingu offre una semplice soluzione online per l'area di lavoro che esegue applicazioni legacy, SaaS e documenti da un browser html5. Di conseguenza rende disponibili tutte le applicazioni in modo sicuro in qualsiasi tipo di dispositivo. Per i servizi SaaS è disponibile un'ampia gamma di opzioni Single Sign-On. Inoltre è possibile integrare profondamente diversi file system (cloud) nell'area di lavoro. Oltre alla mobilità totale, l'area di lavoro online avanzata di Awingu fornirà una sicurezza ottimale con controlli granulari (ad esempio download/caricamento), controllo sull'utilizzo completo, Multi-Factor Authentication (ad esempio Azure Multi-Factor Authentication), registrazione delle sessioni e altro ancora. Awingu consente in modo predefinito la condivisione dei documenti e delle sessioni di applicazione per una collaborazione ottimizzata e sicura.
La soluzione Awingu è multi-tenant, multi-AD e open API. È utilizzata da piccole e grandi aziende, provider di servizi cloud e [ISV](http://www.isv2saas.com). Questi clienti apprezzano soprattutto la facilità di utilizzo e installazione e il basso TCO.

Awingu All-in-One è [disponibile in Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/awingu.awingu-arm) con 2 utenti contemporanei integrati. Sono disponibili licenze aggiuntive tramite un'[ampia gamma di distributori e rivenditori](http://www.awingu.com/reseller).

Altre informazioni sono disponibili nell'articolo [Looking for an Azure RemoteApp alternative?](http://alternative-for-azure-remoteapp.awingu.com/) (Stai cercando un'alternativa a RemoteApp?).


> Sede principale: Belgio
> 
> Aree operative: EMEA, America del Nord e Brasile
> 
> Offerta di soluzioni desktop e RemoteApp basate sulla sessione: sì, entrambe 
> 
> **Globale**:
> 
> Arnaud Marlière, CMO
> 
> E-mail: [arnaud@awingu.com](mailto:arnaud@awingu.com)
> 
> Telefono: +1 646 583 3025
> 
> **Sede centrale in Belgio**:
> 
> Ottergemsesteenweg-Zuid 808 B44
> 
> 9000 Gent
> 
> E-mail: [info@awingu.com](mailto:info@awingu.com) 
> 
> Telefono: +32 9 296 40 11
> 
> **USA**:
> 
> 7th floor, 1177 Ave of the Americas,
> 
> New York, NY 10036
> 
> E-mail: [info.us@awingu.com](mailto:info.us@awingu.com)

### <a name="citrix-xenapp-essentials-released-april-2017"></a>Citrix XenApp Essentials (rilasciato nell'aprile 2017)
Ora disponibile in [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Citrix.XenAppEssentials), Citrix XenApp Essentials è il nuovo servizio di virtualizzazione delle applicazioni che combina la potenza e flessibilità della piattaforma Citrix Cloud con la visione semplice, prescrittiva e facile da usare di Microsoft Azure RemoteApp.  

I clienti di Azure RemoteApp esistenti possono [registrarsi per una prova gratuita](https://www.citrix.com/global-partners/microsoft/remote-app.html).  Nota: solo il servizio utente Citrix è gratuito, verranno addebitati i costi di calcolo e archiviazione di Azure

Altre informazioni su [Citrix XenApp Essentials](https://www.citrix.com/global-partners/microsoft/remote-app.html).

### <a name="citrix-cloud-xenapp-service-and-xendesktop-service"></a>Servizio Citrix Cloud XenApp e servizio XenDesktop 

[Servizio Citrix Cloud XenApp e servizio XenDesktop](https://www.citrix.com/products/citrix-cloud/services.html) è la soluzione migliore per la fornitura sia di applicazioni che di desktop, oltre a capacità avanzate di gestione e monitoraggio. 

### <a name="citrix-service-provider-program"></a>Programma Citrix Service Provider
Il programma Citrix Service Provider facilita i provider di servizi nell'offrire alle piccole e medie imprese la semplicità del cloud computing virtuale, racchiudendo tutti i servizi desiderati in un agevole modello con pagamento in base al consumo. I provider di servizi Citrix possono accrescere il business relativo al contratto Microsoft SPLA ed espandere gli investimenti nella piattaforma dei Servizi Desktop remoto con qualsiasi dispositivo, l'accesso da postazioni remote, il supporto per il maggior numero di applicazioni, un'esperienza avanzata e livelli superiori di sicurezza e scalabilità. A loro volta, gli stessi provider di servizi Citrix possono attrarre più sottoscrittori, aumentare la soddisfazione dei clienti e ridurre i costi operativi. [Leggere altre informazioni](http://www.citrix.com/products/service-providers.html) o [cercare un partner](https://www.citrix.com/buy/partnerlocator.html).

### <a name="frame"></a>Frame

Organizzazioni IT in enterprise ed enti pubblici, fornitori di servizi gestiti e fornitori leader di software scelgono Frame per creare e gestire nel cloud aree di lavoro software-defined protette. Dalle piccole alle grandi organizzazioni, Frame rende incredibilmente semplice l'accesso degli utenti alle applicazioni Windows in qualsiasi browser e da qualsiasi dispositivo. La piattaforma Frame include tutto ciò che serve a un amministratore per distribuire le applicazioni dal cloud, inclusa l'infrastruttura di Azure e le licenze RDS (usare il proprio account Azure e le proprie licenze è facoltativo). 

Altre informazioni su [Frame in Azure](https://www.fra.me/ara). 

> Località primaria: San Mateo, CA, USA
>
> Regione operativa: tutto il mondo
>
> Partner Microsoft: Sì
> 
> Telefono: 1-480-269-4668

### <a name="microsoft-hosted-service-provider"></a>Provider di servizi ospitati Microsoft
I partner di hosting offrono in genere un servizio per desktop e applicazioni Windows ospitato completamente gestito, che può includere la gestione delle risorse di Azure, dei sistemi operativi, delle applicazioni e del supporto tecnico tramite i contratti di licenza stipulati fra il partner e Microsoft e altri fornitori di software, in aggiunta a un contratto di licenza per provider di servizi che disciplina la rivendita di licenze SAL (Subscriber Access License). Le informazioni seguenti forniscono tutti i dettagli e i contatti di alcuni provider di servizi di hosting specializzati nell'assistere i clienti durante la migrazione di Azure RemoteApp. Consultare l'[elenco aggiornato dei provider di servizi ospitati](http://aka.ms/rdsonazurecertified) che hanno portato a termine il percorso di apprendimento e la valutazione su RDS in IaaS.  

#### <a name="aspex"></a>ASPEX
[ASPEX](http://www.aspex.be/en) fornisce servizi agli ISV che desiderano passare al cloud e ottimizzare le configurazioni cloud esistenti. ASPEX offre un'ampia gamma di servizi gestiti, DevOps e di consulenza.  

> Sede principale: Anversa
> 
> Regione operativa: Europa occidentale
> 
> Stato partner: [Silver](https://partnercenter.microsoft.com/pcv/solution-providers/aspex_9397f5dd-ebdd-405b-b926-19a5bda61f7a/cfe00bac-ea36-4591-a60b-ec001c4c3dff)
> 
> Provider di servizi cloud Microsoft: sì
> 
> Offerta di soluzioni desktop e RemoteApp basate sulla sessione: sì, entrambe
> 
> Soluzioni per la migrazione di Azure RemoteApp: sì, [altre informazioni](https://www.aspex.be/en/azure-remote-apps)
> 
> Telefono: +3232202198
> 
> E-mail: [info@aspex.be](mailto:info@aspex.be)
> 
> Web: [http://cloud.aspex.be/contact-ara-0](http://cloud.aspex.be/contact-ara-0)

#### <a name="conexlink-platform-name-mycloudit"></a>Conexlink (nome della piattaforma: MyCloudIT)
[MyCloudIT](https://mycloudit.com) è una piattaforma di automazione che consente alle aziende IT di semplificare, ottimizzare e ridimensionare la migrazione e l'erogazione di desktop remoti, applicazioni remote e infrastrutture nel cloud di Microsoft Azure. 

La piattaforma MyCloudIT riduce i tempi di distribuzione del 95% e i costi di Azure del 30%, trasferendo nel cloud l'intera infrastruttura IT di ogni cliente in poche sequenze di tasti. Ora i partner possono gestire i clienti da un unico dashboard globale, assistere gli utenti finali del servizio in tutto il mondo come mai prima d'ora e aumentare i ricavi senza aggiungere costi generali o richiedere una formazione completa su Azure.  

> Sede principale: Dallas (Texas)
> 
> Regione operativa: tutto il mondo
> 
> Stato partner: [Gold](https://partnercenter.microsoft.com/pcv/solution-providers/conexlink_4298787366/843036_1?k=Conexlink)
> 
> Provider di servizi cloud Microsoft: sì
> 
> Offerta di soluzioni desktop e RemoteApp basate sulla sessione: sì, entrambe
> 
> Soluzioni per la migrazione di Azure RemoteApp: sì, [altre informazioni](https://mycloudit.com/remote-app-microsoft/)
> 
> Brian Garoutte, Vicepresidente dell'area Business Development
> 
> Telefono: 972-218-0741
>   
> E-mail: [brian.garoutte@conexlink.com](mailto:brian.garoutte@conexlink.com)

#### <a name="acuutech"></a>Acuutech
[Acuutech](http://www.acuutech.com) è specializzata nel fornire soluzioni desktop in hosting e distribuire ai clienti di tutto il mondo esperienze complete per desktop e applicazioni ISV basate su tecnologie Microsoft da Azure e dai propri data center.

> Sede principale: Londra; Singapore; Houston (Texas)
> 
> Regione operativa: tutto il mondo
> 
> Stato partner: Gold
> 
> Provider di servizi cloud Microsoft: sì
> 
> Offerta di soluzioni desktop e RemoteApp basate sulla sessione: sì, entrambe
> 
> Soluzioni per la migrazione di Azure RemoteApp: sì, [altre informazioni](http://www.acuutech.com/ara-migration/)
> 
> **Regno Unito**:
>   
> 5/6 York House, Langston Road,
>   
> Loughton, Essex IG10 3TQ
>   
> Telefono: +44 (0) 20 8502 2155
> 
> **Singapore**:
>   
> 100 Cecil Street, #09-02, 
>   
> The Globe, Singapore 069532
> 
> Telefono: +65 6709 4933
>   
> **America del Nord**:
>   
> 3601 S. Sandman St.
>   
> Suite 200, Houston, TX 77098
>   
> Telefono: +1 713 691 0800

#### <a name="saasplaza"></a>**SaaSplaza**
[SaaSplaza](http://www.saasplaza.com/) offre un portfolio Microsoft Dynamics completo (NAV, AX, GP, SL, CRM) con cloud privato e pubblico (Azure).

> Località primaria: Paesi Bassi
> 
> Area operativa: tutto il mondo
> 
> Stato partner: [Gold](https://partnercenter.microsoft.com/pcv/solution-providers/saasplaza_4295495801/791011_2?k=saasplaza)
> 
> Provider di servizi cloud Microsoft: sì
> 
> Offerta di soluzioni desktop e RemoteApp basate sulla sessione: sì, entrambe
> 
> **EMEA**:
> 
> Prins Mauritslaan 29-35
> 
> 71 LP Badhoevedorp
> 
> Paesi Bassi
> 
> Telefono: +31 20 547 8060 
> 
>  **Americhe**:
> 
> 171 Saxony Road, Suite 105
> 
> Encinitas, CA 92024
> 
> San Diego
> 
> Stati Uniti
> 
> Telefono: +1 858 385 8900 
> 
> **APAC**:
> 
> 105 Cecil Street
>    
> \#11-08, The Octagon
> 
> Singapore 069534
> 
> Singapore
>   
> Telefono - Singapore: +65 6222 6591
> 
> Telefono - Australia: +61 2 8310 5568 
>    
> Telefono - Nuova Zelanda: +64 4 488 0321
> 
## <a name="need-more-help"></a>Ulteriore assistenza
Per farsi aiutare nella scelta o se si hanno altre domande, usare uno dei metodi seguenti. 

1. Scrivere un'e-mail all'indirizzo [arainfo@microsoft.com](mailto:arainfo@microsoft.com).
2. Contattare il [supporto tecnico di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Aprire un [caso di supporto su Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
3. Contattare il [team di vendita locale](https://azure.microsoft.com/overview/sales-number/).


