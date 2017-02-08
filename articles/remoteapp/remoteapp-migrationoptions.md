---
title: Opzioni per la migrazione da Azure RemoteApp | Documentazione Microsoft
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
ms.date: 01/07/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 0af5a4e2139a202c7f62f48c7a7e8552457ae76d
ms.openlocfilehash: d12ccdc13d6964a6de8068a63f945c7eac40b682


---
# <a name="options-for-migrating-out-of-azure-remoteapp"></a>Opzioni per la migrazione da Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp sta per essere sospeso. Per i dettagli, vedere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Se si è interrotto l'uso di Azure RemoteApp dopo l'[annuncio del ritiro](https://go.microsoft.com/fwlink/?linkid=821148) o perché è terminato il periodo di valutazione, è necessario eseguire la migrazione da Azure RemoteApp a un altro servizio app. La migrazione può essere effettuata in due modi: con una distribuzione autogestita (spesso denominata Infrastructure as a Service [IaaS]) o tramite un'offerta completamente gestita (spesso denominata Platform as a Service oppure Software as a Service [PaaS/SaaS]). 

L'approccio IaaS self-service consiste in una distribuzione "fai da te" gestita dall'utente e di sua proprietà, direttamente distribuita su macchine virtuali (VM) o sistemi fisici. Altrimenti, un'offerta PaaS/SaaS completamente gestita è più simile ad Azure RemoteApp: un partner fornisce un livello di servizio in una soluzione .NET di comunicazione remota che gestisce gli aspetti operativi e di manutenzione, mentre il cliente si occupa di gestire app e immagini.

Continuare a leggere per ulteriori informazioni, tra cui gli esempi delle diverse opzioni di hosting.    

## <a name="self-managed-iaas-solutions"></a>Soluzioni autogestite (IaaS)
### <a name="rds-on-iaas"></a>**RDS su IaaS**
È possibile implementare una distribuzione di Servizi Desktop remoto (in Windows Server) basata su una sessione nativa tramite RemoteApp o desktop locali oppure in un ambiente ospitato (come le VM di Azure). Le distribuzioni di Servizi Desktop remoto su IaaS sono particolarmente indicate per i clienti che le conoscono già e che possiedono le necessarie competenze tecniche in merito. 

> [!NOTE]
> Per poter usufruire di questa opzione di distribuzione, è necessario disporre di un contratto multilicenza con Software Assurance (SA) per le licenze CAL per Servizi Desktop remoto.
> 
> 

Distribuire Servizi Desktop remoto nelle VM di Azure è più semplice che mai se si usano i modelli di distribuzione e applicazione delle patch (leggere una [panoramica](https://blogs.technet.microsoft.com/enterprisemobility/2015/07/13/azure-resource-manager-template-for-rds-deployment/) e quindi [accedere ai modelli](https://aka.ms/rdautomation)). Le stesse funzionalità di scalabilità elastica si ottengono con il modello di distribuzione classica di Azure (non il modello di risorse di Azure) all'interno di Azure RemoteApp tramite lo [script per la scalabilità automatica](https://gallery.technet.microsoft.com/scriptcenter/Automatic-Scaling-of-9b4f5e76), pur essendo disponibili altre personalizzazioni e configurazioni. Quando si distribuiscono i Servizi Desktop remoto su VM di Azure, è possibile avvalersi del [supporto tecnico di Azure](https://azure.microsoft.com/support/plans/), gestito dagli stessi professionisti che forniscono assistenza per Azure RemoteApp. Per una stima dei costi basata sull'uso esistente, è possibile contattare il [supporto tecnico di Azure](https://azure.microsoft.com/support/plans/) oppure eseguire il calcolo autonomamente usando l'apposito strumento presto disponibile.  Inoltre, con le VM serie N (attualmente in Private Preview), è possibile aggiungere vGPU. Per informazioni su come aggiungere vGPU e [sfruttare i miglioramenti dei Servizi Desktop remoto in Windows Server 2016](https://myignite.microsoft.com/videos/2794), è disponibile una sessione introduttiva.   

Per un aiuto in fase di distribuzione, è possibile consultare le guide dettagliate alla distribuzione di [Windows Server 2012 R2](http://aka.ms/rdsonazure) e [Windows Server 2016](http://aka.ms/rdsonazure2016). Per conoscere tutte le novità, consultare il [blog su Desktop remoto](https://blogs.technet.microsoft.com/enterprisemobility/?product=windows-server-remote-desktop-services).

### <a name="citrix-on-iaas"></a>**Citrix in IaaS**
Una distribuzione Citrix nativa di XenApp o XenDesktop in base alla sessione può essere effettuata in locale o in un ambiente ospitato (ad esempio, nelle VM di Azure). 

Per altre informazioni, consultare la guida dettagliata alla distribuzione [Citrix XA 7.6 in Azure](http://www.citrixandmicrosoft.com/Documents/Citrix-Azure Deployment Guide-v.1.0.docx). Sono inoltre disponibili altre informazioni su [Citrix in Azure](http://www.citrixandmicrosoft.com/Solutions/AzureCloud.aspx), con in più il calcolatore dei prezzi. Infine, è possibile discutere le opzioni disponibili con un [contatto Citrix](http://citrix.com/English/contact/index.asp).

## <a name="fully-managed-paassaas-offerings"></a>Offerte completamente gestite (PaaS/SaaS)
### <a name="citrix-cloud"></a>**Citrix Cloud**
[Soluzione cloud Citrix esistente](https://www.citrix.com/products/citrix-cloud/), identica a Citrix XenApp Express dal punto di vista dell'architettura. Citrix offre una [promozione con sconto del&50;%](https://www.citrix.com/blogs/2016/10/03/special-promotion-for-microsoft-azure-remoteapp-customers/) per i clienti esistenti di Azure RemoteApp. 

### <a name="citrix-xenapp-express-in-tech-preview"></a>**Citrix XenApp Express (Technical Preview)**
[Registrarsi alla Technical preview](http://now.citrix.com/remoteapp) e assistere alla [sessione introduttiva](https://myignite.microsoft.com/videos/2792) (a partire dal minuto 20:30). Sul piano dell'architettura, XenApp Express è identico a Citrix Cloud, ad eccezione di una gestione semplificata dell'interfaccia utente e di altre caratteristiche simili ad Azure RemoteApp. 

Ulteriori informazioni su [Citrix XenApp Express](http://now.citrix.com/remoteapp).   

### <a name="citrix-service-provider-program"></a>**Programma Citrix Service Provider**
Il programma Citrix Service Provider facilita i provider di servizi nell'offrire alle piccole e medie imprese la semplicità del cloud computing virtuale, racchiudendo tutti i servizi desiderati in un agevole modello con pagamento in base al consumo. I provider di servizi Citrix possono accrescere il business relativo al contratto Microsoft SPLA ed espandere gli investimenti nella piattaforma dei Servizi Desktop remoto con qualsiasi dispositivo, l'accesso da postazioni remote, il supporto per il maggior numero di applicazioni, un'esperienza avanzata e livelli superiori di sicurezza e scalabilità. A loro volta, gli stessi provider di servizi Citrix possono attrarre più sottoscrittori, aumentare la soddisfazione dei clienti e ridurre i costi operativi. [Leggere altre informazioni](http://www.citrix.com/products/service-providers.html) o [cercare un partner](https://www.citrix.com/buy/partnerlocator.html).

### <a name="microsoft-hosted-service-provider"></a>**Provider di servizi ospitati Microsoft**
I partner di hosting offrono in genere un servizio per desktop e applicazioni Windows ospitato completamente gestito, che può includere la gestione delle risorse di Azure, dei sistemi operativi, delle applicazioni e del supporto tecnico tramite i contratti di licenza stipulati fra il partner e Microsoft e altri fornitori di software, in aggiunta a un contratto di licenza per provider di servizi che disciplina la rivendita di licenze SAL (Subscriber Access License). Le informazioni seguenti forniscono tutti i dettagli e i contatti di alcuni provider di servizi di hosting specializzati nell'assistere i clienti durante la migrazione di Azure RemoteApp. Consultare l'[elenco aggiornato dei provider di servizi ospitati](http://aka.ms/rdsonazurecertified) che hanno portato a termine il percorso di apprendimento e la valutazione su RDS in IaaS.  

#### <a name="aspex"></a>**ASPEX**
[ASPEX](http://www.aspex.be/en) fornisce servizi agli ISV che desiderano passare al cloud e ottimizzare le configurazioni cloud esistenti. ASPEX offre un'ampia gamma di servizi gestiti, DevOps e di consulenza.  

Sede principale: Anversa

Regione operativa: Europa occidentale

Stato partner: [Silver](https://partnercenter.microsoft.com/pcv/solution-providers/aspex_9397f5dd-ebdd-405b-b926-19a5bda61f7a/cfe00bac-ea36-4591-a60b-ec001c4c3dff)

Provider di servizi cloud Microsoft: sì

Offerta di soluzioni desktop e RemoteApp basate sulla sessione: sì, entrambe

Soluzioni per la migrazione di Azure RemoteApp: sì, [altre informazioni](https://www.aspex.be/en/azure-remote-apps)

**Contatto:**

* Telefono: +3232202198
* E-mail: [info@aspex.be](mailto:info@aspex.be)
* Web: [http://cloud.aspex.be/contact-ara-0](http://cloud.aspex.be/contact-ara-0)

#### <a name="conexlink-platform-name-mycloudit"></a>**Conexlink (nome della piattaforma: MyCloudIT)**
[MyCloudIT](http://www.mycloudit.com) è una piattaforma di automazione che consente alle aziende IT di semplificare, ottimizzare e ridimensionare la migrazione e l'erogazione di desktop remoti, applicazioni remote e infrastrutture nel cloud di Microsoft Azure. 

La piattaforma MyCloudIT riduce i tempi di distribuzione del 95% e i costi di Azure del 30%, trasferendo nel cloud l'intera infrastruttura IT di ogni cliente in poche sequenze di tasti. Ora i partner possono gestire i clienti da un unico dashboard globale, assistere gli utenti finali del servizio in tutto il mondo come mai prima d'ora e aumentare i ricavi senza aggiungere costi generali o richiedere una formazione completa su Azure.  

Sede principale: Dallas (Texas)

Regione operativa: tutto il mondo

Stato partner: [Gold](https://partnercenter.microsoft.com/pcv/solution-providers/conexlink_4298787366/843036_1?k=Conexlink)

Provider di servizi cloud Microsoft: sì

Offerta di soluzioni desktop e RemoteApp basate sulla sessione: sì, entrambe

Soluzioni per la migrazione di Azure RemoteApp: sì, [altre informazioni](https://mycloudit.com/remote-app-microsoft/)

**Contatto:**

* Brian Garoutte, Vicepresidente dell'area Business Development
  
   Telefono: 972-218-0741
  
   E-mail: [brian.garoutte@conexlink.com](mailto:brian.garoutte@conexlink.com)

#### <a name="acuutech"></a>**Acuutech**
[Acuutech](http://www.acuutech.com) è specializzata nel fornire soluzioni desktop in hosting e distribuire ai clienti di tutto il mondo esperienze complete per desktop e applicazioni ISV basate su tecnologie Microsoft da Azure e dai propri data center.

Sede principale: Londra; Singapore; Houston (Texas)

Regione operativa: tutto il mondo

Stato partner: Gold

Provider di servizi cloud Microsoft: sì

Offerta di soluzioni desktop e RemoteApp basate sulla sessione: sì, entrambe

Soluzioni per la migrazione di Azure RemoteApp: sì, [altre informazioni](http://www.acuutech.com/ara-migration/)

**Contatto:**

* Regno Unito:
  
  5/6 York House, Langston Road,
  
  Loughton, Essex IG10 3TQ
  
  Telefono: +44 (0) 20 8502 2155
* Singapore:
  
  100 Cecil Street, #09-02, 
  
  The Globe, Singapore 069532
  
  Telefono: +65 6709 4933
* America del Nord: 
  
  3601 S. Sandman St.
  
  Suite 200, Houston, TX 77098
  
  Telefono: +1 713 691 0800

#### <a name="saasplaza"></a>**SaaSplaza**
[SaaSplaza](http://www.saasplaza.com/) offre un portfolio Microsoft Dynamics completo (NAV, AX, GP, SL, CRM) con cloud privato e pubblico (Azure).

Località primaria: Paesi Bassi

Area operativa: tutto il mondo

Stato partner: [Gold](https://partnercenter.microsoft.com/pcv/solution-providers/saasplaza_4295495801/791011_2?k=saasplaza)

Provider di servizi cloud Microsoft: sì

Offerta di soluzioni desktop e RemoteApp basate sulla sessione: sì, entrambe

**Contatto:**

- Europa, Medio Oriente e Africa:

   Prins Mauritslaan 29-35

   71 LP Badhoevedorp

   Paesi Bassi

   Telefono: +31 20 547 8060 

- Americhe:

   171 Saxony Road, Suite 105

   Encinitas, CA 92024

   San Diego

   Stati Uniti

   Telefono: +1 858 385 8900 

- Asia Pacifico:

   105 Cecil Street
   
   \#11-08, The Octagon

   Singapore 069534

   Singapore
   
   Telefono - Singapore: +65 6222 6591

   Telefono - Australia: +61 2 8310 5568 
   
   Telefono - Nuova Zelanda: +64 4 488 0321

## <a name="need-more-help"></a>Ulteriore assistenza
Per farsi aiutare nella scelta o se si hanno altre domande, usare uno dei metodi seguenti. 

1. Scrivere un'e-mail all'indirizzo [arainfo@microsoft.com](mailto:arainfo@microsoft.com).
2. Contattare il [supporto tecnico di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Aprire un [caso di supporto su Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
3. Contattare il [team di vendita locale](https://azure.microsoft.com/overview/sales-number/).




<!--HONumber=Dec16_HO2-->


