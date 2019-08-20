---
title: Rilevamento delle minacce per il livello di servizio di Azure nel centro sicurezza di Azure | Microsoft Docs
description: Questo argomento presenta gli avvisi del livello di servizio di Azure disponibili nel centro sicurezza di Azure.
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
ms.date: 8/18/2019
ms.author: v-mohabe
ms.openlocfilehash: 18579f716f80cdf79b703dc78fe520c675e82c51
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575625"
---
# <a name="threat-detection-for-azure-service-layer-in-azure-security-center"></a>Rilevamento delle minacce per il livello di servizio di Azure nel centro sicurezza di Azure

Questo argomento presenta gli avvisi del Centro sicurezza disponibili per il monitoraggio dei seguenti livelli di servizio di Azure.

* [Livello rete di Azure](#network-layer)
* [Livello di gestione di Azure (Azure Resource Manager) (anteprima)](#management-layer)

>[!NOTE]
>Usando i dati di telemetria usati dal centro sicurezza per sfruttare i feed interni di Azure, le analisi fornite di seguito sono applicabili a tutti i tipi di risorse.

## Livello rete di Azure<a name="network-layer"></a>

L'analisi a livello di rete del Centro sicurezza si basa su [dati IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)di esempio, ovvero le intestazioni dei pacchetti raccolte dai router core di Azure. Basati su questo feed di dati, i modelli di Machine Learning del Centro sicurezza identificano e contrassegnano le attività di traffico dannoso. Per arricchire gli indirizzi IP, il Centro sicurezza utilizza il database di intelligence per le minacce di Microsoft.

> [!div class="mx-tableFixed"]

|Avviso|Descrizione|
|---|---|
|**Attività di rete RDP in uscita sospetta**|L'analisi del traffico di rete campionata ha rilevato una comunicazione in uscita Remote Desktop Protocol (RDP) anomala originata da una risorsa nella distribuzione. Questa attività viene considerata anomala per questo ambiente e può indicare che la risorsa è stata compromessa e viene ora utilizzata per la forza bruta dell'endpoint RDP esterno. Si noti che in seguito a questo tipo di attività l'IP può venire contrassegnato come dannoso dalle entità esterne.|
|**Attività di rete RDP in uscita sospetta verso più destinazioni**|L'analisi del traffico di rete campionata ha rilevato una comunicazione in uscita Remote Desktop Protocol (RDP) anomala originata da una risorsa nella distribuzione a più destinazioni. Questa attività viene considerata anomala per questo ambiente e può indicare che la risorsa è stata compromessa e viene ora utilizzata per la forza bruta degli endpoint RDP esterni. Si noti che in seguito a questo tipo di attività l'IP può venire contrassegnato come dannoso dalle entità esterne.|
|**Attività di rete SSH in uscita sospetta**|L'analisi del traffico di rete campionata ha rilevato una comunicazione Secure Shell (SSH) in uscita anomala originata da una risorsa nella distribuzione. Questa attività viene considerata anomala per questo ambiente e può indicare che la risorsa è stata compromessa e viene ora usata per la forza bruta dell'endpoint SSH esterno. Si noti che in seguito a questo tipo di attività l'IP può venire contrassegnato come dannoso dalle entità esterne.|
|**Attività di rete SSH in uscita sospetta verso più destinazioni**|L'analisi del traffico di rete campionata ha rilevato una comunicazione Secure Shell (SSH) in uscita anomala originata da una risorsa nella distribuzione a più destinazioni. Questa attività viene considerata anomala per questo ambiente e può indicare che la risorsa è stata compromessa e viene ora usata per la forza bruta degli endpoint SSH esterni. Si noti che in seguito a questo tipo di attività l'IP può venire contrassegnato come dannoso dalle entità esterne.|
|**Attività di rete SSH in ingresso sospetta da più origini**|L'analisi del traffico di rete campionata ha rilevato una comunicazione SSH in ingresso anomala da più origini a una risorsa nella distribuzione. Diversi indirizzi IP univoci che si connettono alla risorsa sono considerati anomali per questo ambiente. Questa attività può indicare un tentativo di forza bruta dell'interfaccia SSH da più host (botnet).|
|**Attività di rete SSH in ingresso sospetta**|L'analisi del traffico di rete campionata ha rilevato una comunicazione SSH in ingresso anomala a una risorsa nella distribuzione. Un numero relativamente elevato di connessioni in ingresso alla risorsa viene considerato anomalo per questo ambiente. Questa attività può indicare un tentativo di forza bruta dell'interfaccia SSH.
|**Attività di rete RDP in ingresso sospetta da più origini**|L'analisi del traffico di rete campionata ha rilevato una comunicazione RDP in ingresso anomala da più origini a una risorsa nella distribuzione. Diversi indirizzi IP univoci che si connettono alla risorsa sono considerati anomali per questo ambiente. Questa attività può indicare un tentativo di forza bruta dell'interfaccia RDP da più host (botnet).|
|**Attività di rete RDP in ingresso sospetta**|L'analisi del traffico di rete campionata ha rilevato una comunicazione RDP in ingresso anomala a una risorsa nella distribuzione. Un numero relativamente elevato di connessioni in ingresso alla risorsa viene considerato anomalo per questo ambiente. Questa attività può indicare un tentativo di forza bruta dell'interfaccia SSH.|
|**È stata rilevata la comunicazione di rete con un indirizzo dannoso**|L'analisi del traffico di rete campionata ha rilevato la comunicazione originata da una risorsa nella distribuzione con un possibile server di comando e controllo (C & C). Si noti che in seguito a questo tipo di attività l'IP può venire contrassegnato come dannoso dalle entità esterne.|

Per comprendere in che modo il Centro sicurezza può usare i segnali correlati alla rete per applicare la protezione dalle minacce, vedere [rilevamento di DNS euristici nel centro sicurezza di Azure](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).
## Livello di gestione di Azure (Azure Resource Manager) (anteprima)<a name ="management-layer"></a>

>[!NOTE]
>Il livello di protezione del Centro sicurezza basato su Azure Resource Manager è attualmente in fase di anteprima.

Il Centro sicurezza offre un livello di protezione aggiuntivo sfruttando Azure Resource Manager eventi, considerato il piano di controllo per Azure. Analizzando i record di Azure Resource Manager, il Centro sicurezza rileva operazioni insolite o potenzialmente dannose nell'ambiente della sottoscrizione di Azure.

> [!div class="mx-tableFixed"]

|Avviso|Descrizione|
|---|---|
|**Esecuzione del Toolkit di microesplosioni**|Nell'ambiente è stata rilevata un'esecuzione nota del Toolkit di esplorazione dell'ambiente cloud. Lo strumento "microrompi" ( https://github.com/NetSPI/MicroBurst) vedere può essere usato da un utente malintenzionato (o tester di penetrazione) per eseguire il mapping delle risorse delle sottoscrizioni, identificare le configurazioni non sicure e perdere informazioni riservate.|
|**Esecuzione di azzurrite Toolkit**|Nell'ambiente è stata rilevata un'esecuzione nota del Toolkit di esplorazione dell'ambiente cloud. Lo strumento "azzurrite" (vedere https://github.com/mwrlabs/Azurite) può essere usato da un utente malintenzionato (o tester di penetrazione) per eseguire il mapping delle risorse delle sottoscrizioni e identificare le configurazioni non protette.|
|**Sessione di gestione sospetta utilizzando un account inattivo**|L'analisi dei log attività della sottoscrizione ha rilevato un comportamento sospetto. Entità non utilizzata per un lungo periodo di tempo, sta ora eseguendo azioni che possono proteggere la persistenza per un utente malintenzionato.|
|**Sessione di gestione sospetta con PowerShell**|L'analisi dei log attività della sottoscrizione ha rilevato un comportamento sospetto. Un'entità che non usa regolarmente PowerShell per gestire l'ambiente di sottoscrizione ora usa PowerShell ed esegue azioni che possono proteggere la persistenza per un utente malintenzionato.|
|**Uso di tecniche avanzate di persistenza di Azure**|L'analisi dei log attività della sottoscrizione ha rilevato un comportamento sospetto. Ai ruoli personalizzati sono state assegnate entità di identità legittime. In questo modo, l'autore dell'attacco può ottenere persistenza in un ambiente cliente di Azure.|
|**Attività da un paese non frequente**|Si è verificata un'attività da un percorso non recente o mai visitato da alcun utente nell'organizzazione.<br/>Questo rilevamento prende in considerazione le località di attività passate per determinare località nuove e non frequenti. Il motore di rilevamento delle anomalie archivia le informazioni sulle località precedenti usate dagli utenti dell'organizzazione. 
|**Attività da indirizzi IP anonimi**|È stata rilevata l'attività degli utenti da un indirizzo IP identificato come indirizzo IP proxy anonimo. <br/>Questi proxy vengono usati da persone che vogliono nascondere l'indirizzo IP del dispositivo e possono essere usati per attacchi dannosi. Questo rilevamento sfrutta un algoritmo di apprendimento automatico che riduce i "falsi positivi", ad esempio gli indirizzi IP contrassegnati in modo errato che vengono usati diffusamente dagli utenti dell'organizzazione.|
|**È stata rilevata una corsa Impossibile**|Si sono verificate due attività utente, ovvero una o più sessioni, che hanno originato da località geograficamente distanti entro un periodo di tempo più breve rispetto al tempo impiegato dall'utente per spostarsi dalla prima posizione al secondo. Indica che un altro utente utilizza le stesse credenziali. <br/>Questo rilevamento si avvale di un algoritmo di Machine Learning che ignora i "falsi positivi" evidenti che contribuiscono alle condizioni di viaggio impossibili, ad esempio le VPN e le posizioni usate regolarmente da altri utenti nell'organizzazione. Questo rilevamento prevede un periodo di apprendimento iniziale di sette giorni durante il quale apprende lo schema di attività di un nuovo di utente.|

>[!NOTE]
> Alcune delle analisi precedenti sono basate su Microsoft Cloud App Security (MCAS). Per trarre vantaggio da queste analisi, è necessaria una licenza MCAS attivata. Se si dispone di una licenza MCAS, questi avvisi sono abilitati per impostazione predefinita. Per disabilitarli:
>
> 1. Nel pannello del Centro sicurezza selezionare **Criteri di sicurezza**. Per la sottoscrizione che si vuole modificare fare clic su **Modifica impostazioni**.
> 2. Fare clic su **Rilevamento delle minacce**.
> 3. In **Enable integrations** (Abilita integrazioni) deselezionare **Allow Microsoft Cloud App Security to access my data** (Consenti a Cloud App Security di accedere ai miei dati) e fare clic su **Salva**.

>[!NOTE]
>Il Centro sicurezza di Azure archivia i dati dei clienti correlati alla sicurezza nella stessa area geografica della risorsa. Se Microsoft non ha ancora distribuito il Centro sicurezza di Azure nell'area geografica della risorsa, archivia i dati nel Stati Uniti. Quando Microsoft Cloud App Security (MCAS) è abilitata, queste informazioni vengono archiviate in base alle regole di localizzazione geografica di MCAS. [Per ulteriori informazioni, vedere archiviazione dei dati per servizi non locali](http://azuredatacentermap.azurewebsites.net/).
