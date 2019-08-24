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
ms.openlocfilehash: 5458d26172123f0f6cb9914a04ec05b3163a084f
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013282"
---
# <a name="threat-detection-for-the-azure-service-layer-in-azure-security-center"></a>Rilevamento delle minacce per il livello di servizio di Azure nel centro sicurezza di Azure

Questo argomento presenta gli avvisi del Centro sicurezza di Azure disponibili quando si monitorano i livelli di servizio di Azure seguenti:

* [Livello rete di Azure](#network-layer)
* [Livello di gestione di Azure (Azure Resource Manager) (anteprima)](#management-layer)

>[!NOTE]
>Le analisi seguenti sono applicabili a tutti i tipi di risorse. Usano i dati di telemetria che il Centro sicurezza offre toccando i feed interni di Azure.

## Livello rete di Azure<a name="network-layer"></a>

L'analisi a livello di rete del Centro sicurezza si basa su [dati IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)di esempio, ovvero le intestazioni dei pacchetti raccolte dai router core di Azure. Basati su questo feed di dati, i modelli di Machine Learning del Centro sicurezza identificano e contrassegnano le attività di traffico dannoso. Per arricchire gli indirizzi IP, il Centro sicurezza usa il database di intelligence per le minacce di Microsoft.

> [!div class="mx-tableFixed"]

|Avviso|DESCRIZIONE|
|---|---|
|**Attività di rete RDP in uscita sospetta**|L'analisi del traffico di rete campionata ha rilevato una comunicazione in uscita Remote Desktop Protocol (RDP) anomala, originata da una risorsa nella distribuzione. Questa attività viene considerata anomala per questo ambiente. Potrebbe indicare che la risorsa è stata compromessa e viene ora utilizzata per l'attacco di forza bruta a un endpoint RDP esterno. Si noti che questo tipo di attività potrebbe causare la segnalazione dell'indirizzo IP come dannoso da entità esterne.|
|**Attività di rete RDP in uscita sospetta verso più destinazioni**|L'analisi del traffico di rete campionata ha rilevato una comunicazione RDP in uscita anomala, originata da una risorsa nella distribuzione in più destinazioni. Questa attività viene considerata anomala per questo ambiente. Potrebbe indicare che la risorsa è stata compromessa e viene ora utilizzata per l'attacco di forza bruta agli endpoint RDP esterni. Si noti che questo tipo di attività potrebbe causare la segnalazione dell'indirizzo IP come dannoso da entità esterne.|
|**Attività di rete SSH in uscita sospetta**|L'analisi del traffico di rete campionata ha rilevato una comunicazione in uscita Secure Shell (SSH) anomala, originata da una risorsa nella distribuzione. Questa attività viene considerata anomala per questo ambiente. Potrebbe indicare che la risorsa è stata compromessa e viene ora usata per l'attacco di forza bruta a un endpoint SSH esterno. Si noti che questo tipo di attività potrebbe causare la segnalazione dell'indirizzo IP come dannoso da entità esterne.|
|**Attività di rete SSH in uscita sospetta verso più destinazioni**|L'analisi del traffico di rete campionata ha rilevato una comunicazione SSH in uscita anomala, originata da una risorsa nella distribuzione in più destinazioni. Questa attività viene considerata anomala per questo ambiente. Potrebbe indicare che la risorsa è stata compromessa e viene ora usata per attacchi di forza bruta per gli endpoint SSH esterni. Si noti che questo tipo di attività potrebbe causare la segnalazione dell'indirizzo IP come dannoso da entità esterne.|
|**Attività di rete SSH in ingresso sospetta da più origini**|L'analisi del traffico di rete campionata ha rilevato una comunicazione SSH in ingresso anomala da più origini a una risorsa nella distribuzione. Diversi indirizzi IP univoci che si connettono alla risorsa sono considerati anomali per questo ambiente. Questa attività potrebbe indicare un tentativo di attacco di forza bruta all'interfaccia SSH da più host (botnet).|
|**Attività di rete SSH in ingresso sospetta**|L'analisi del traffico di rete campionata ha rilevato una comunicazione SSH in ingresso anomala a una risorsa nella distribuzione. Un numero relativamente elevato di connessioni in ingresso alla risorsa viene considerato anomalo per questo ambiente. Questa attività potrebbe indicare un tentativo di attacco di forza bruta all'interfaccia SSH.
|**Attività di rete RDP in ingresso sospetta da più origini**|L'analisi del traffico di rete campionata ha rilevato una comunicazione RDP in ingresso anomala da più origini a una risorsa nella distribuzione. Diversi indirizzi IP univoci che si connettono alla risorsa sono considerati anomali per questo ambiente. Questa attività potrebbe indicare un tentativo di attacco di forza bruta all'interfaccia RDP da più host (botnet).|
|**Attività di rete RDP in ingresso sospetta**|L'analisi del traffico di rete campionata ha rilevato una comunicazione RDP in ingresso anomala a una risorsa nella distribuzione. Un numero relativamente elevato di connessioni in ingresso alla risorsa viene considerato anomalo per questo ambiente. Questa attività potrebbe indicare un tentativo di attacco di forza bruta all'interfaccia SSH.|
|**È stata rilevata la comunicazione di rete con un indirizzo dannoso**|L'analisi del traffico di rete campionata ha rilevato la comunicazione originata da una risorsa nella distribuzione con un possibile server di comando e controllo (C & C). Si noti che in seguito a questo tipo di attività l'IP può venire contrassegnato come dannoso dalle entità esterne.|

Per comprendere in che modo il Centro sicurezza può usare i segnali correlati alla rete per applicare la protezione dalle minacce, vedere [rilevamento di DNS euristici nel centro sicurezza di Azure](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).

>[!NOTE]
>Gli avvisi di rilevamento delle minacce a livello di rete di Azure, nel centro sicurezza di Azure, non vengono generati durante la prima ora dopo la creazione della macchina virtuale.

## Livello di gestione di Azure (Azure Resource Manager) (anteprima)<a name ="management-layer"></a>

>[!NOTE]
>Il livello di protezione del Centro sicurezza basato su Azure Resource Manager è attualmente in fase di anteprima.

Il Centro sicurezza offre un ulteriore livello di protezione usando gli eventi Azure Resource Manager, che è considerato il piano di controllo per Azure. Analizzando i record di Azure Resource Manager, il Centro sicurezza rileva operazioni insolite o potenzialmente dannose nell'ambiente della sottoscrizione di Azure.

> [!div class="mx-tableFixed"]

|Avviso|Descrizione|
|---|---|
|**Esecuzione del Toolkit di microesplosioni**|Nell'ambiente è stata rilevata un'esecuzione nota del Toolkit di esplorazione dell'ambiente cloud. Il [microimpulso](https://github.com/NetSPI/MicroBurst) dello strumento può essere usato da un utente malintenzionato (o tester di penetrazione) per eseguire il mapping delle risorse delle sottoscrizioni, identificare le configurazioni non sicure e perdere informazioni riservate.|
|**Esecuzione di azzurrite Toolkit**|Nell'ambiente è stata rilevata un'esecuzione nota del Toolkit di esplorazione dell'ambiente cloud. Lo strumento [azzurrite](https://github.com/mwrlabs/Azurite) può essere usato da un utente malintenzionato (o tester di penetrazione) per eseguire il mapping delle risorse delle sottoscrizioni e identificare le configurazioni non sicure.|
|**Sessione di gestione sospetta utilizzando un account inattivo**|L'analisi dei log attività della sottoscrizione ha rilevato un comportamento sospetto. Un'entità non utilizzata per un lungo periodo di tempo sta ora eseguendo azioni che possono proteggere la persistenza per un utente malintenzionato.|
|**Sessione di gestione sospetta con PowerShell**|L'analisi dei log attività della sottoscrizione ha rilevato un comportamento sospetto. Un'entità che non usa regolarmente PowerShell per gestire l'ambiente di sottoscrizione ora usa PowerShell ed esegue azioni che possono proteggere la persistenza per un utente malintenzionato.|
|**Uso di tecniche avanzate di persistenza di Azure**|L'analisi dei log attività della sottoscrizione ha rilevato un comportamento sospetto. Ai ruoli personalizzati sono state assegnate entità di identità legittime. In questo modo, l'autore dell'attacco può ottenere persistenza in un ambiente cliente di Azure.|
|**Attività da un paese non frequente**|Si è verificata un'attività da una località che non è stata visitata di recente o mai da un utente nell'organizzazione.<br/>Questo rilevamento prende in considerazione le località di attività passate per determinare località nuove e non frequenti. Il motore di rilevamento delle anomalie archivia le informazioni sulle località precedenti usate dagli utenti dell'organizzazione. 
|**Attività da indirizzi IP anonimi**|È stata rilevata l'attività degli utenti da un indirizzo IP identificato come indirizzo IP proxy anonimo. <br/>Questi proxy vengono usati da persone che vogliono nascondere l'indirizzo IP del dispositivo e possono essere usati per finalità dannose. Questo rilevamento usa un algoritmo di apprendimento automatico che riduce i falsi positivi, ad esempio gli indirizzi IP con tag errato che sono ampiamente usati dagli utenti dell'organizzazione.|
|**È stata rilevata una corsa Impossibile**|Si sono verificate due attività utente (in una o più sessioni), provenienti da località geograficamente distanti. Questo problema si verifica entro un periodo di tempo più breve rispetto al tempo impiegato dall'utente per spostarsi dalla prima posizione al secondo. Indica che un altro utente utilizza le stesse credenziali. <br/>Questo rilevamento usa un algoritmo di apprendimento automatico che ignora i falsi positivi evidenti che contribuiscono alle condizioni di viaggio impossibili, ad esempio le VPN e le posizioni usate regolarmente da altri utenti nell'organizzazione. Il rilevamento ha un periodo di apprendimento iniziale di sette giorni, durante il quale apprende il modello di attività di un nuovo utente.|

>[!NOTE]
> Alcune delle analisi precedenti sono basate su Microsoft Cloud App Security. Per trarre vantaggio da queste analisi, è necessario attivare una licenza Cloud App Security. Se si dispone di una licenza di Cloud App Security, questi avvisi sono abilitati per impostazione predefinita. Per disabilitarli:
>
> 1. Nel pannello **Centro sicurezza** selezionare criteri di **sicurezza**. Per la sottoscrizione che si vuole modificare, selezionare **Modifica impostazioni**.
> 2. Selezionare **rilevamento minacce**.
> 3. In **Abilita integrazioni**deselezionare **Consenti Microsoft cloud app Security per accedere ai dati**e selezionare **Salva**.

>[!NOTE]
>Il Centro sicurezza archivia i dati dei clienti correlati alla sicurezza nella stessa area geografica della risorsa. Se Microsoft non ha ancora distribuito il Centro sicurezza nell'area geografica della risorsa, archivia i dati nel Stati Uniti. Quando Cloud App Security è abilitata, queste informazioni vengono archiviate in base alle regole di posizione geografica di Cloud App Security. Per altre informazioni, vedere [archiviazione dei dati per servizi non a livello di](https://azuredatacentermap.azurewebsites.net/)area.
