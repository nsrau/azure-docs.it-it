---
title: Configurare il servizio Cloud App Discovery in Azure Active Directory | Microsoft Docs
description: Individuare e gestire le applicazioni con Cloud App Discovery per fornire informazioni interattive sull'uso del cloud e sullo shadow IT.
services: active-directory
keywords: cloud app discovery, gestione delle applicazioni
documentationcenter: 
author: curtand
manager: mtillman
tags: ignite
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: curtand
ms.reviewer: nigu
ms.openlocfilehash: 4a0cb1b7793c846f98ae4e89b99b4bda984cd5e4
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2017
---
# <a name="set-up-cloud-app-discovery-in-azure-ad"></a>Configurare Cloud App Discovery in Azure AD

Cloud App Discovery in Azure AD ora si basa sull'integrazione dei dati resi disponibili da Microsoft Cloud App Security. Per fornire informazioni aggiornate sull'uso del cloud e sullo shadow IT, Cloud App Discovery confronta i log sul traffico con il catalogo di Cloud App Security di oltre 15.000 app cloud. In questo articolo viene descritto il processo di configurazione e vengono forniti collegamenti alle informazioni dettagliate per ogni passaggio. Vengono inoltre descritte le informazioni su proxy e firewall e il supporto dei file di log.

## <a name="prerequisites"></a>Prerequisiti

* Per poter usare il prodotto, l'organizzazione deve avere una licenza di Azure AD Premium P1. Per altre informazioni, vedere [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).
* Per configurare Cloud App Discovery, è necessario essere un amministratore globale o avere un ruolo con autorizzazioni di lettura per la sicurezza in Azure Active Directory.

## <a name="setup-steps"></a>Procedura di configurazione

1. [Configurare report snapshot](cloudappdiscovery-set-up-snapshots.md) per verificare il formato del log e assicurarsi che i log forniscano informazioni utilizzabili in Cloud App Discovery. Forniscono anche visibilità ad hoc dei log sul traffico caricati manualmente dai firewall e dai server proxy.

2. [Configurare report continui](https://docs.microsoft.com/cloud-app-security/discovery-docker) per analizzare tutti i log inoltrati dalla rete usando l'agente di raccolta log di Cloud App Security. È possibile usarli per identificare le nuove app e le tendenze di utilizzo.

3. Se i log non sono attualmente supportati, [configurare un parser di log personalizzato](https://docs.microsoft.com/cloud-app-security/custom-log-parser), in modo che Cloud App Discovery possa analizzarli.
  
## <a name="log-processing-flow"></a>Flusso di elaborazione dei log

La generazione di report può richiedere da pochi minuti a diverse ore, a seconda della quantità di dati. Ecco che cosa avviene:

* **Caricamento** I log sul traffico Web dalla rete vengono caricati nel portale.
* **Elaborazione tramite parser** Cloud App Security analizza ed estrae i dati sul traffico dai log sul traffico con un parser dedicato per ogni origine dati.
* **Analisi** I dati sul traffico vengono analizzati confrontandoli con il catalogo di Cloud App Security per identificare più di 15.000 app cloud. Anche gli indirizzi IP e gli utenti attivi vengono identificati nell'ambito dell'analisi.
* **Generazione del report** Cloud App Security genera un report dei dati estratti dai file di log e lo rende disponibile per Cloud App Discovery.

> [!NOTE]
> * I dati dei report continui vengono analizzati due volte al giorno.
> * L'agente di raccolta log comprime i dati prima del caricamento. Il traffico in uscita nell'agente di raccolta log è circa il 10% delle dimensioni dei log sul traffico ricevuti.

## <a name="using-traffic-logs-for-cloud-app-discovery"></a>Uso di log sul traffico per Cloud App Discovery

Cloud App Discovery Usa i dati contenuti nei log sul traffico. Più dettagli è possibile aggiungere nel log, migliore sarà la visibilità ottenuta. Cloud App Discovery richiede dati sul traffico Web con gli attributi seguenti:

* Data della transazione
* Indirizzo IP di origine
* Utente di origine, **consigliato**
* Indirizzo IP di destinazione
* URL di destinazione, **consigliato**. Gli URL assicurano una maggiore accuratezza per il rilevamento delle app cloud rispetto agli indirizzi IP.
* Quantità totale di dati
* Quantità di dati caricati o scaricati, per informazioni approfondite sui modelli di utilizzo delle app cloud
* Azione intrapresa (elementi consentiti/bloccati)

Cloud App Discovery non può visualizzare o analizzare gli attributi non inclusi nei log. Ad esempio, il formato dei log standard dei **firewall Cisco ASA** non contiene la **quantità di byte caricati per ogni transazione**, il **nome utente** o l'**URL di destinazione** , ma solo l'indirizzo IP di destinazione. Di conseguenza, si potrebbe ottenere una visibilità minore delle app cloud da questa origine dati. Per i firewall Cisco ASA, impostare il livello di informazioni su 6.1.

Per generare correttamente un report di Cloud App Discovery, i log sul traffico devono soddisfare i requisiti seguenti:

1.  L'origine dati è [un firewall o un server proxy supportato](#supported-firewalls-and-proxies).
2.  Il formato dei log corrisponde al formato standard previsto. Questa condizione viene controllata in fase di caricamento. Per ottimizzare il formato dei log, vedere [Creare report snapshot di Cloud App Discovery](cloudappdiscovery-set-up-snapshots.md).
3.  Gli eventi non risalgono a più di 90 giorni prima.
4.  Il file di log è valido e include informazioni sul traffico in uscita.

## <a name="supported-firewalls-and-proxy-servers"></a>Firewall e server proxy supportati

* Barracuda - Firewall di app Web (W3C)
* Blue Coat Proxy SG - Log di accesso (W3C)
* Punto di controllo
* FirePOWER ASA Cisco
* Firewall Cisco ASA (per i firewall Cisco ASA, impostare il livello di informazioni su 6)
* Cisco IronPort WSA
* Cisco ScanSafe
* Cisco Meraki - Log degli URL
* Clavister NGFW (syslog)
* Dell SonicWALL
* Fortinet FortiGate
* Juniper SRX
* Juniper SSG
* McAfee Secure Web Gateway
* Microsoft Forefront Threat Management Gateway (W3C)
* Firewall serie Palo Alto
* Sophos SG
* Sophos Cyberoam
* SQUID (comune)
* SQUID (nativo)
* Websense - Web Security Solutions - Report dettagliato di analisi (CSV)
* Websense - Web Security Solutions - Log attività Internet (CEF)
* Zscaler

> [!NOTE]
> Cloud App Discovery supporta indirizzi IPv4 e IPv6.

Se il log non è supportato, selezionare **Altro** come **Origine dati** e specificare il dispositivo e il log che si sta tentando di caricare. Il log viene esaminato dal team di analisti cloud di Cloud App Security. Quando viene aggiunto il supporto per il tipo di log, viene inviata una notifica, ma è anche possibile definire un parser personalizzato che corrisponda al formato di log. Per altre informazioni, vedere [Usare un parser di log personalizzato](https://docs.microsoft.com/cloud-app-security/custom-log-parser).

## <a name="data-attributes-according-to-vendor-documentation"></a>Attributi dei dati (in base alla documentazione del fornitore)

| Origine dati         | URL dell'app di destinazione | Indirizzo IP dell'app di destinazione | Username | Indirizzo IP di origine | Traffico totale | Byte caricati |
|-----------------------------------------|----------------|---------------|----------|-----------|---------------|----------------|
| Barracuda                               | **Sì**        | **Sì**       | **Sì**  | **Sì**   | No             | No              |
| Blue Coat                               | **Sì**        | No             | **Sì**  | **Sì**   | **Sì**       | **Sì**        |
| Checkpoint                              | No              | **Sì**       | No        | **Sì**   | No             | No              |
| Cisco ASA                               | No              | **Sì**       | No        | **Sì**   | **Sì**       | No              |
| Cisco FWSM                              | No              | **Sì**       | No        | **Sì**   | **Sì**       | No              |
| Cisco IronPort WSA                      | **Sì**        | **Sì**       | **Sì**  | **Sì**   | **Sì**       | **Sì**        |
| Cisco Meraki                            | **Sì**        | **Sì**       | No        | **Sì**   | No             | No              |
| Clavister NGFW (syslog)                 | **Sì**        | **Sì**       | **Sì**  | **Sì**   | **Sì**       | **Sì**        |
| Dell SonicWALL                          | **Sì**        | **Sì**       | No        | **Sì**   | **Sì**       | **Sì**        |
| FortiGate                               | No              | **Sì**       | No        | **Sì**   | **Sì**       | **Sì**        |
| Juniper SRX                             | No              | **Sì**       | No        | **Sì**   | **Sì**       | **Sì**        |
| Juniper SSG                             | No              | **Sì**       | No        | **Sì**   | **Sì**       | **Sì**        |
| McAfee SWG                              | **Sì**        | No             | No        | **Sì**   | **Sì**       | **Sì**        |
| Microsoft Forefront Threat Management Gateway                                  | **Sì**        | No             | **Sì**  | **Sì**   | **Sì**       | **Sì**        |
| Palo Alto Networks                      | **Sì**        | **Sì**       | **Sì**  | **Sì**   | **Sì**       | **Sì**        |
| Sophos                                  | **Sì**        | **Sì**       | **Sì**  | **Sì**   | **Sì**       | No              |
| SQUID (comune)                          | **Sì**        | No             | **Sì**  | **Sì**   | No             | **Sì**        |
| SQUID (nativo)                          | **Sì**        | No             | **Sì**  | **Sì**   | No             | **Sì**        |
| Websense - Report di analisi (CSV)   | **Sì**        | **Sì**       | **Sì**  | **Sì**   | **Sì**       | **Sì**        |
| Websense - Log attività Internet (CEF)  | **Sì**        | **Sì**       | **Sì**  | **Sì**   | **Sì**       | **Sì**        |
| Zscaler                                 | **Sì**        | **Sì**       | **Sì**  | **Sì**   | **Sì**       | **Sì**        |


## <a name="next-steps"></a>Passaggi successivi
Usare i collegamenti seguenti per continuare a configurare la versione di Cloud App Discovery in Azure AD.

* [Creare report di snapshot](cloudappdiscovery-set-up-snapshots.md)
* [Configurare la creazione di report continua](https://docs.microsoft.com/cloud-app-security/discovery-docker)
* [Usare un parser di log personalizzato](https://docs.microsoft.comcommit/cloud-app-security/custom-log-parser)
