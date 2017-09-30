---
title: Configurare Cloud App Discovery in Azure Active Directory | Microsoft Docs
description: Fornisce informazioni sull'individuazione e la gestione delle applicazioni con Cloud App Discovery, quali sono i vantaggi e il relativo funzionamento.
services: active-directory
keywords: cloud app discovery, gestione delle applicazioni
documentationcenter: 
author: curtand
manager: femila
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: curtand
ms.reviewer: nigu
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 2943e0b119726b31dd6e6507d279f6c8f74df98b
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="get-started-using-cloud-app-discovery-in-azure-ad"></a>Introduzione all'uso di Cloud App Discovery in Azure AD

La versione di anteprima pubblica dei miglioramenti di Cloud App Discovery in Azure AD è ora disponibile per i titolari di una licenza di Azure Active Directory Premium P1. Questi miglioramenti sono basati sull'integrazione con Microsoft Cloud App Security. Cloud App Discovery confronta i log sul traffico con il catalogo di Cloud App Security di oltre 15.000 app cloud per fornire informazioni aggiornate sull'uso del cloud e sullo shadow IT. 

## <a name="prerequisites"></a>Prerequisiti

Per poter usare il prodotto, l'organizzazione deve avere una licenza di Azure AD Premium P1. Per altre informazioni, vedere [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

Per configurare Cloud App Discovery, è necessario essere un amministratore globale o avere un ruolo con autorizzazioni di lettura per la sicurezza in Azure Active Directory. Un utente cui è assegnato un ruolo amministrativo ha le stesse autorizzazioni per tutte le app cloud sottoscritte dall'organizzazione.

## <a name="snapshot-and-continuous-reports"></a>Report snapshot e continui

È possibile generare due tipi di report:

* I **report snapshot** forniscono visibilità ad hoc dei log sul traffico caricati manualmente dai firewall e dai server proxy. È possibile usarli per assicurarsi che i log forniscano informazioni utilizzabili a Cloud App Discovery.

* I **report continui** analizzano tutti i log inoltrati dalla rete usando l'[agente di raccolta log di Cloud App Security](https://docs.microsoft.com/cloud-app-security/discovery-docker). È possibile usarli per identificare le nuove app e le tendenze di utilizzo.

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

| Origine dati         | URL dell'app di destinazione | Indirizzo IP dell'app di destinazione | Nome utente | Indirizzo IP di origine | Traffico totale | Byte caricati |
|-----------------------------------------|----------------|---------------|----------|-----------|---------------|----------------|
| Barracuda                               | **Sì**        | **Sì**       | **Sì**  | **Sì**   | No            | No             |
| Blue Coat                               | **Sì**        | No            | **Sì**  | **Sì**   | **Sì**       | **Sì**        |
| Checkpoint                              | No             | **Sì**       | No       | **Sì**   | No            | No             |
| Cisco ASA                               | No             | **Sì**       | No       | **Sì**   | **Sì**       | No             |
| Cisco FWSM                              | No             | **Sì**       | No       | **Sì**   | **Sì**       | No             |
| Cisco IronPort WSA                      | **Sì**        | **Sì**       | **Sì**  | **Sì**   | **Sì**       | **Sì**        |
| Cisco Meraki                            | **Sì**        | **Sì**       | No       | **Sì**   | No            | No             |
| Clavister NGFW (syslog)                 | **Sì**        | **Sì**       | **Sì**  | **Sì**   | **Sì**       | **Sì**        |
| Dell SonicWALL                          | **Sì**        | **Sì**       | No       | **Sì**   | **Sì**       | **Sì**        |
| FortiGate                               | No             | **Sì**       | No       | **Sì**   | **Sì**       | **Sì**        |
| Juniper SRX                             | No             | **Sì**       | No       | **Sì**   | **Sì**       | **Sì**        |
| Juniper SSG                             | No             | **Sì**       | No       | **Sì**   | **Sì**       | **Sì**        |
| McAfee SWG                              | **Sì**        | No            | No       | **Sì**   | **Sì**       | **Sì**        |
| Microsoft Forefront Threat Management Gateway                                  | **Sì**        | No            | **Sì**  | **Sì**   | **Sì**       | **Sì**        |
| Palo Alto Networks                      | **Sì**        | **Sì**       | **Sì**  | **Sì**   | **Sì**       | **Sì**        |
| Sophos                                  | **Sì**        | **Sì**       | **Sì**  | **Sì**   | **Sì**       | No             |
| SQUID (comune)                          | **Sì**        | No            | **Sì**  | **Sì**   | No            | **Sì**        |
| SQUID (nativo)                          | **Sì**        | No            | **Sì**  | **Sì**   | No            | **Sì**        |
| Websense - Report di analisi (CSV)   | **Sì**        | **Sì**       | **Sì**  | **Sì**   | **Sì**       | **Sì**        |
| Websense - Log attività Internet (CEF)  | **Sì**        | **Sì**       | **Sì**  | **Sì**   | **Sì**       | **Sì**        |
| Zscaler                                 | **Sì**        | **Sì**       | **Sì**  | **Sì**   | **Sì**       | **Sì**        |


## <a name="next-steps"></a>Passaggi successivi
Usare i collegamenti seguenti per continuare a configurare la versione migliorata di Cloud App Discovery in Azure AD.

* [Creare report snapshot di Cloud App Discovery](cloudappdiscovery-set-up-snapshots.md)
* [Configurare il caricamento automatico dei log per i report continui](https://docs.microsoft.com/cloud-app-security/discovery-docker)
* [Usare un parser di log personalizzato](https://docs.microsoft.comcommit/cloud-app-security/custom-log-parser)
