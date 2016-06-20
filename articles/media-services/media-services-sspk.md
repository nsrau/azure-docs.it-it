<properties 
	pageTitle="Licenza per Microsoft® Smooth Streaming Client Porting Kit" 
	description="Informazioni su come ottenere la licenza per Microsoft® Smooth Streaming Client Porting Kit." 
	services="media-services" 
	documentationCenter="" 
	authors="xpouyat,vsood" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/06/2016"  
	ms.author="xpouyat"/>

#Licenza per Microsoft® Smooth Streaming Client Porting Kit

##Panoramica

Microsoft Smooth Streaming Client Porting Kit, **SSPK** per brevità, è un'implementazione client di Smooth Streaming ottimizzata per aiutare i produttori di dispositivi integrati, operatori di telefonia via cavo e mobile, provider di servizi di gestione del contenuto, produttori di telefoni, fornitori di software indipendenti (ISV) e provider di soluzioni a creare prodotti e servizi per la trasmissione di flussi adattivi in formato Smooth Streaming. SSPK è un'implementazione indipendente dal dispositivo e dalla piattaforma del client Smooth Streaming che può essere applicata dal licenziatario a qualsiasi dispositivo e piattaforma.

Di seguito è riportata un'architettura di alto livello che include la casella IIS Smooth Streaming Porting Kit, ovvero l'implementazione client di Smooth Streaming fornita da Microsoft, e tutta la logica di base per la riproduzione di contenuto Smooth Streaming. Questa implementazione viene quindi applicata dai partner a una piattaforma o un dispositivo specifico mediante l'implementazione di interfacce appropriate.

![SSPK](./media/media-services-sspk/sspk-arch.png)

##Descrizione

SSPK è concesso in licenza a condizioni che offrono un valore aziendale eccellente. La licenza SSPK offre al settore:

- Codice sorgente di Smooth Streaming Porting Kit in C++ 
  - implementa la funzionalità client di Smooth Streaming Client
  - aggiunge analisi del formato, euristica, logica di buffering e così via.
- API dell'applicazione lettore 
  -	interfacce di programmazione per l'interazione con un'applicazione lettore multimediale
- Interfaccia Platform Abstraction Layer (PAL) 
  -	interfacce di programmazione per l'interazione con il sistema operativo (thread, socket)
- Interfaccia Hardware Abstraction Layer (HAL) 
  -	interfacce di programmazione per l'interazione con un decodificatore A/V hardware (decodifica, rendering)
- Interfaccia Digital Rights Management (DRM) 
  -	interfacce di programmazione per la gestione di DRM tramite DRM Abstraction Layer (DAL)
  -	Microsoft PlayReady Porting Kit viene fornito separatamente, ma si integra tramite questa interfaccia. Per altri dettagli sulle licenze Microsoft PlayReady Device, fare clic [qui](http://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl).
- Esempi di implementazione 
  -	implementazione PAL di esempio per Linux
  -	implementazione HAL di esempio per GStreamer

##Opzioni di licenza

Microsoft Smooth Streaming Client Porting Kit è disponibile per i licenziatari con due contratti di licenza distinti: uno per lo sviluppo di prodotti provvisori per il client Smooth Streaming e un altro per la distribuzione di prodotti finali per il client Smooth Streaming agli utenti finali.
 
- Per i produttori di chipset, gli integratori di sistema o i fornitori di software indipendenti (ISV) che necessitano di un kit per il porting del codice sorgente per sviluppare prodotti provvisori, è consigliabile usare una **Interim Product License** di Microsoft Smooth Streaming Client Porting Kit.
- Per i produttori di dispositivi o gli ISV che necessitano di diritti di distribuzione di prodotti finali per il client Smooth Streaming agli utenti finali, è consigliabile usare una **Final Product License** di Microsoft Smooth Streaming Client Porting Kit.

###Interim Product License di Microsoft Smooth Streaming Client Porting Kit

In base alle condizioni di questa licenza, Microsoft offre Smooth Streaming Client Porting Kit e i diritti di proprietà intellettuale necessari per sviluppare e distribuire prodotti provvisori per il client Smooth Streaming ad altri licenziatari di dispositivi Smooth Streaming Client Porting Kit che distribuiscono prodotti finali per il client Smooth Streaming.

####Struttura tariffaria

Una tariffa di licenza unica di USD 50.000 fornisce l'accesso a Smooth Streaming Client Porting Kit.

###Final Product License di Microsoft Smooth Streaming Client Porting Kit

In base alle condizioni di questa licenza, Microsoft offre tutti i diritti di proprietà intellettuale necessari per ricevere prodotti provvisori per il client Smooth Streaming da altri licenziatari di Smooth Streaming Client Porting Kit e per distribuire prodotti finali con personalizzazione della società per il client Smooth Streaming agli utenti finali.

####Struttura tariffaria

Smooth Streaming Client Final Product è disponibile in un modello a pagamento come segue:

- USD 0,10 per ogni implementazione di dispositivo fornita
- I diritti sono limitati a un massimo di USD 50.000 all'anno
- Nessun diritto per le prime 10.000 implementazioni di dispositivi ogni anno 

##Procedura di gestione delle licenze e accesso a SSPK

Per domande relative alla gestione delle licenze, inviare un messaggio di posta elettronica a [sspkinfo@microsoft.com](mailto:sspkinfo@microsoft.com).

Il [portale di distribuzione di SSPK](https://microsoft.sharepoint.com/teams/SSPKDOWNLOAD/) è accessibile a licenziatari della versione Interim registrati.

I licenziatari di SSPK Interim e Final possono inviare domande tecniche a [smoothpk@microsoft.com](mailto:smoothpk@microsoft.com).

##Licenziatari di contratti Microsoft Smooth Streaming Client Interim Product

- Adroit Business Solutions, Inc
- Advanced Digital Broadcast SA
- AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
- Albis Technologies Ltd.
- Alticast Corporation
- Amazon Digital Services, Inc.
- AVC Multimedia Software Co., Ltd.
- EchoStar Purchasing Corporation
- Enseo, Inc.
- Fluendo S.A.
- HANDAN BroadInfoCom Co., Ltd.
- Infomir GMBH
- Irdeto USA Inc.
- Liberty Global Services BV
- MediaTek Inc.
- MStar Co, Ltd.
- Nintendo Co., Ltd.
- OpenTV, Inc.
- Saffron Digital Limited
- Sichuan Changhong Electric Co., Ltd
- SoftAtHome
- Tatung Technology Inc.
- Vestel Elektronik Sanayi ve Ticaret A.S.
- VisualOn, Inc.
- ZTE Corporation

##Licenziatari di contratti Microsoft Smooth Streaming Client Final Product

- Advanced Digital Broadcast SA
- AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
- Albis Technologies Ltd.
- Amazon Digital Services, Inc.
- AmTRAN Technology Co., Ltd.
- Arcadyan Technology Corporation
- ATMACA ELEKTRONİK SAN. VE TİC. A.Ş
- British Sky Broadcasting Limited
- CastPal Technology Inc., Shenzhen
- Compal Electronics, Inc.
- Dongguan Digital AV Technology Corp., Ltd.
- EchoStar Purchasing Corporation
- Enseo, Inc.
- Filmflex Movies Limited
- Fluendo S.A.
- Gibson Innovations Limited
- HANDAN BroadInfoCom Co., Ltd.
- Hisense International Co., Ltd
- Homecast Co.,Ltd
- Hon Hai Precision Industry Co., Ltd.
- Infomir GMBH
- Kaonmedia Co., Ltd.
- KDDI Corporation
- Nintendo Co., Ltd.
- Orange SA
- Saffron Digital Limited
- Sagemcom Broadband SAS
- Shenzhen Coship Electronics CO., LTD
- Shenzhen Jiuzhou Electric Co.,Ltd
- Shenzhen Skyworth Digital Technology Co., Ltd
- Sichuan Changhong Electric Co., Ltd.
- Sky Deutschland Fernsehen GmbH & Co. KG
- SmarDTV S.A.
- SoftAtHome
- TCL Overseas Marketing (Macao Commercial Offshore) Limited
- Technicolor Delivery Technologies, SAS
- Toshiba Lifestyle Products & Services Corporation
- Universal Media Corporation /Slovakia/ s.r.o.
- VIZIO, Inc.
- Wistron Corporation
- ZTE Corporation

##Percorsi di apprendimento di Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornire commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0608_2016-->