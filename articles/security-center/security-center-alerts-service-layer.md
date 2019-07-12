---
title: Il rilevamento per il livello di servizio di Azure nel Centro sicurezza di Azure per le minacce | Microsoft Docs
description: Questo argomento presenta avvisi di livello il servizio di Azure disponibili nel Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 8c1733877834f82d9ee2524cf8bf54f532e7d9c4
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571725"
---
# <a name="threat-detection-for-azure-service-layer-in-azure-security-center"></a>Rilevamento delle minacce per il livello di servizio di Azure nel Centro sicurezza di Azure

Questo argomento viene presentato l'avvisi del Centro sicurezza disponibili durante il monitoraggio i seguenti livelli di servizio di Azure.

* [Livello di rete di Azure](#network-layer)
* [Livello di gestione di Azure (Azure Resource Manager) (anteprima)](#management-layer)

>[!NOTE]
>Usa i dati di telemetria che sfrutta il Centro sicurezza di attingere a feed interno di Azure, l'analitica riportati di seguito è applicabili a tutti i tipi di risorse.

## Livello di rete di Azure<a name="network-layer"></a>

Analitica a livello di rete di Centro sicurezza si basata sul campione [dati IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), quali sono le intestazioni dei pacchetti raccolte dai router principale di Azure. Modelli di Centro sicurezza machine learning basato su questo feed di dati, identificano e contrassegno le attività di traffico dannoso. Per arricchire gli indirizzi IP, il Centro sicurezza Usa database di Intelligence sulle minacce di Microsoft.

> [!div class="mx-tableFixed"]

|Avviso|Descrizione|
|---|---|
|**Attività di rete RDP in uscita sospetta**|Analisi del traffico di rete campionati visualizzano rilevato una comunicazione in uscita anomala Remote Desktop Protocol (RDP) che hanno origine da una risorsa nella distribuzione. Questa attività è considerata anomala per questo ambiente e potrebbe indicare che la risorsa sia stata compromessa e viene ora usato per l'endpoint RDP esterni di attacchi di forza bruta. Si noti che in seguito a questo tipo di attività l'IP può venire contrassegnato come dannoso dalle entità esterne.|
|**Attività di rete RDP in più destinazioni in uscita sospetta**|Analisi del traffico di rete campionati visualizzano rilevato una comunicazione in uscita anomala Remote Desktop Protocol (RDP) che hanno origine da una risorsa nella distribuzione a più destinazioni. Questa attività è considerata anomala per questo ambiente e potrebbe indicare che la risorsa sia stata compromessa e viene ora usato per gli endpoint RDP esterni di attacchi di forza bruta. Si noti che in seguito a questo tipo di attività l'IP può venire contrassegnato come dannoso dalle entità esterne.|
|**Attività di rete SSH in uscita sospetta**|Analisi del traffico di rete campionati visualizzano rilevato una comunicazione in uscita anomala Secure Shell (SSH) proveniente da una risorsa nella distribuzione. Questa attività è considerata anomala per questo ambiente e potrebbe indicare che la risorsa sia stata compromessa e viene ora usato per l'endpoint SSH esterni di attacchi di forza bruta. Si noti che in seguito a questo tipo di attività l'IP può venire contrassegnato come dannoso dalle entità esterne.|
|**Attività di rete SSH in più destinazioni in uscita sospetta**|Analisi del traffico di rete campionati visualizzano rilevato una comunicazione in uscita anomala Secure Shell (SSH) proveniente da una risorsa nella distribuzione a più destinazioni. Questa attività è considerata anomala per questo ambiente e potrebbe indicare che la risorsa sia stata compromessa e viene ora usato per gli endpoint SSH esterni di attacchi di forza bruta. Si noti che in seguito a questo tipo di attività l'IP può venire contrassegnato come dannoso dalle entità esterne.|
|**Suspicious attività di rete SSH in ingresso provenienti da più origini**|Analisi del traffico di rete campionati visualizzano rilevato anomale comunicazioni in ingresso SSH da più origini in una risorsa nella distribuzione. Vari indirizzi IP univoco ci si connette per la risorsa è considerato anomalo per questo ambiente. Questa attività può indicare un tentativo di attacco di forza bruta l'interfaccia SSH da più host (Botnet).|
|**Attività di rete SSH in ingresso sospetta**|Analisi del traffico di rete campionati visualizzano ha rilevato una comunicazione SSH in ingresso anomala a una risorsa nella distribuzione. Un numero relativamente elevato di connessioni in ingresso per la risorsa è considerato anomalo per questo ambiente. Questa attività può indicare un tentativo di attacco di forza bruta SSH interfaccia.
|**Suspicious attività di rete RDP in ingresso provenienti da più origini**|Analisi del traffico di rete campionati visualizzano rilevato anomale RDP le comunicazioni in ingresso provenienti da più origini in una risorsa nella distribuzione. Vari indirizzi IP univoco ci si connette per la risorsa è considerato anomalo per questo ambiente. Questa attività può indicare un tentativo di attacco di forza bruta l'interfaccia RDP da più host (Botnet).|
|**Attività di rete RDP in ingresso sospetta**|Analisi del traffico di rete campionati visualizzano ha rilevato una comunicazione RDP in ingresso anomala a una risorsa nella distribuzione. Un numero relativamente elevato di connessioni in ingresso per la risorsa è considerato anomalo per questo ambiente. Questa attività può indicare un tentativo di attacco di forza bruta SSH interfaccia.|

Per capire come usare Centro sicurezza di rete correlati per applicare la protezione delle minacce, vedere i segnali [rilevamenti euristica DNS nel Centro sicurezza Azure](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).
## Livello di gestione di Azure (Azure Resource Manager) (anteprima)<a name ="management-layer"></a>

>[!NOTE]
>Livello di protezione il Centro sicurezza PC basato su Azure Resource Manager è attualmente in anteprima.

Il Centro sicurezza offre un ulteriore livello di protezione dati, sfruttando gli eventi di Azure Resource Manager, che viene considerato il piano di controllo per Azure. Analizzando i record di Azure Resource Manager, il Centro sicurezza rileva operazioni potenzialmente dannose o insolite oppure elementi nell'ambiente di sottoscrizione di Azure.

> [!div class="mx-tableFixed"]

|Avviso|Descrizione|
|---|---|
|**Toolkit microBurst eseguire**|È stato rilevato un toolkit di esplorazione dell'ambiente cloud noto eseguito nell'ambiente in uso. Lo strumento "MicroBurst" (vedere https://github.com/NetSPI/MicroBurst) è utilizzabile da un utente malintenzionato (o tester di penetrazione) per eseguire il mapping delle risorse delle sottoscrizioni, identificare le configurazioni non sicure e determinare una perdita di informazioni riservate.|
|**Toolkit azurite eseguire**|È stato rilevato un toolkit di esplorazione dell'ambiente cloud noto eseguito nell'ambiente in uso. Lo strumento "Azurite" (vedere https://github.com/mwrlabs/Azurite) è utilizzabile da un utente malintenzionato (o tester di penetrazione) per eseguire il mapping delle risorse delle sottoscrizioni e identificare le configurazioni non sicure.|
|**Sessione di gestione sospette usando un account inattivo**|Analisi dei log attività di sottoscrizione ha rilevato un comportamento sospetto. Un'entità che non era in uso per un lungo periodo di tempo, è ora l'esecuzione di azioni che è possono proteggere la persistenza per un utente malintenzionato.|
|**Sessione sospetta gestione usando PowerShell**|Analisi dei log attività di sottoscrizione ha rilevato un comportamento sospetto. Un'entità a cui non regolarmente Usa PowerShell per gestire l'ambiente di sottoscrizione, è ora usando PowerShell e l'esecuzione di azioni che è possono proteggere la persistenza per un utente malintenzionato.|
|**Utilizzare le tecniche avanzate persistenza di Azure**|Analisi dei log attività di sottoscrizione ha rilevato un comportamento sospetto. Ruoli personalizzati sono stati assegnati identità legitimized entità. Questo può causare l'autore dell'attacco a garantirsi in un ambiente del cliente di Azure.|
|**Attività da un paese non frequente**|Si è verificata attività da una posizione che non è stato recentemente o mai visitata da altri utenti dell'organizzazione.<br/>Questo rilevamento prende in considerazione le località di attività passate per determinare località nuove e non frequenti. Il motore di rilevamento delle anomalie archivia le informazioni sulle località precedenti usate dagli utenti dell'organizzazione. 
|**Attività da indirizzi IP anonimi**|Attività degli utenti da un indirizzo IP che è stato identificato come è stato rilevato un indirizzo IP proxy anonimo. <br/>Questi proxy vengono usati da persone che vogliono nascondere l'indirizzo IP del dispositivo e possono essere usati per attacchi dannosi. Questo rilevamento sfrutta un algoritmo di apprendimento automatico che riduce i "falsi positivi", ad esempio gli indirizzi IP contrassegnati in modo errato che vengono usati diffusamente dagli utenti dell'organizzazione.|
|**Comunicazione Impossibile rilevato**|Due attività degli utenti (è una o più sessioni) si sono verificati, provenienti da località geograficamente distanti e realizzate entro un periodo di tempo più breve del tempo avrebbe richiesto all'utente di spostarsi dalla prima località alla seconda. Ciò indica che un altro utente sta usando le stesse credenziali. <br/>Questo rilevamento si avvale di un algoritmo di machine learning che ignora "falsi positivi più evidenti" aggiunta come contributo alle condizioni di tipo comunicazione Impossibile, ad esempio le VPN e posizioni usate regolarmente da altri utenti nell'organizzazione. Questo rilevamento prevede un periodo di apprendimento iniziale di sette giorni durante il quale apprende lo schema di attività di un nuovo di utente.|

>[!NOTE]
> Numerosi l'analitica riportato sopra sono supportate da Microsoft Cloud App Security (MCAS). Per trarre vantaggio da queste analitica, è necessaria una licenza MCAS attivata. Se si ha una licenza MCAS, questi avvisi sono abilitati per impostazione predefinita. Per disattivare questa funzione:
>
> 1. Nel pannello del Centro sicurezza selezionare **Criteri di sicurezza**. Per la sottoscrizione che si vuole modificare fare clic su **Modifica impostazioni**.
> 2. Fare clic su **Rilevamento delle minacce**.
> 3. In **Enable integrations** (Abilita integrazioni) deselezionare **Allow Microsoft Cloud App Security to access my data** (Consenti a Cloud App Security di accedere ai miei dati) e fare clic su **Salva**.

>[!NOTE]
>Centro sicurezza di Azure archivia i dati dei clienti relative alla sicurezza nella stessa area geografica relativo della risorsa. Se Microsoft non ha ancora distribuito Centro sicurezza di Azure nell'area geografica della risorsa, quindi Archivia i dati negli Stati Uniti. Quando Microsoft Cloud App Security (MCAS) è abilitata, queste informazioni vengono archiviate in conformità con le regole di posizione geografica di MCAS. Visualizzare [archiviazione di dati per i servizi non a livello di area per ulteriori informazioni](http://azuredatacentermap.azurewebsites.net/).
