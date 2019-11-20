---
title: Rilevamento delle minacce a livello di servizio di Azure-Centro sicurezza di Azure
description: Questo argomento presenta gli avvisi del livello di servizio di Azure disponibili nel centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 08/25/2019
ms.author: memildin
ms.openlocfilehash: 7db9f50b4fb1a9309737f05db13a914f414372ed
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186510"
---
# <a name="threat-detection-for-the-azure-service-layer-in-azure-security-center"></a>Rilevamento delle minacce per il livello di servizio di Azure nel centro sicurezza di Azure

Questo argomento presenta gli avvisi del Centro sicurezza di Azure disponibili quando si monitorano i livelli di servizio di Azure seguenti:

* [Livello rete di Azure](#network-layer)
* [Livello di gestione di Azure (Azure Resource Manager) (anteprima)](#management-layer)
* [Insieme di credenziali delle chiavi di Azure](#azure-keyvault)

>[!NOTE]
>Le analisi seguenti sono applicabili a tutti i tipi di risorse. Usano i dati di telemetria che il Centro sicurezza offre toccando i feed interni di Azure.

## Livello rete di Azure<a name="network-layer"></a>

L'analisi a livello di rete del Centro sicurezza si basa su [dati IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)di esempio, ovvero le intestazioni dei pacchetti raccolte dai router core di Azure. Basati su questo feed di dati, i modelli di Machine Learning del Centro sicurezza identificano e contrassegnano le attività di traffico dannoso. Per arricchire gli indirizzi IP, il Centro sicurezza usa il database di intelligence per le minacce di Microsoft.

> [!div class="mx-tableFixed"]

|Avviso|DESCRIZIONE|
|---|---|
|**Attività di rete RDP in uscita sospetta**|L'analisi del traffico di rete campionata ha rilevato una comunicazione in uscita Remote Desktop Protocol (RDP) anomala, originata da una risorsa nella distribuzione. Questa attività viene considerata anomala per questo ambiente. Potrebbe indicare che la risorsa è stata compromessa e viene ora utilizzata per l'attacco di forza bruta a un endpoint RDP esterno. Questo tipo di attività potrebbe causare la segnalazione dell'indirizzo IP come dannoso da entità esterne.|
|**Attività di rete RDP in uscita sospetta verso più destinazioni**|L'analisi del traffico di rete campionata ha rilevato una comunicazione RDP in uscita anomala, originata da una risorsa nella distribuzione in più destinazioni. Questa attività viene considerata anomala per questo ambiente. Potrebbe indicare che la risorsa è stata compromessa e viene ora utilizzata per l'attacco di forza bruta agli endpoint RDP esterni. Questo tipo di attività potrebbe causare la segnalazione dell'indirizzo IP come dannoso da entità esterne.|
|**Attività di rete SSH in uscita sospetta**|L'analisi del traffico di rete campionata ha rilevato una comunicazione in uscita Secure Shell (SSH) anomala, originata da una risorsa nella distribuzione. Questa attività viene considerata anomala per questo ambiente. Potrebbe indicare che la risorsa è stata compromessa e viene ora usata per l'attacco di forza bruta a un endpoint SSH esterno. Questo tipo di attività potrebbe causare la segnalazione dell'indirizzo IP come dannoso da entità esterne.|
|**Attività di rete SSH in uscita sospetta verso più destinazioni**|L'analisi del traffico di rete campionata ha rilevato una comunicazione SSH in uscita anomala, originata da una risorsa nella distribuzione in più destinazioni. Questa attività viene considerata anomala per questo ambiente. Potrebbe indicare che la risorsa è stata compromessa e viene ora usata per attacchi di forza bruta per gli endpoint SSH esterni. Questo tipo di attività potrebbe causare la segnalazione dell'indirizzo IP come dannoso da entità esterne.|
|**Attività di rete SSH in ingresso sospetta da più origini**|L'analisi del traffico di rete campionata ha rilevato una comunicazione SSH in ingresso anomala da più origini a una risorsa nella distribuzione. Diversi indirizzi IP univoci che si connettono alla risorsa sono considerati anomali per questo ambiente. Questa attività potrebbe indicare un tentativo di attacco di forza bruta all'interfaccia SSH da più host (botnet).|
|**Attività di rete SSH in ingresso sospetta**|L'analisi del traffico di rete campionata ha rilevato una comunicazione SSH in ingresso anomala a una risorsa nella distribuzione. Un numero relativamente elevato di connessioni in ingresso alla risorsa viene considerato anomalo per questo ambiente. Questa attività potrebbe indicare un tentativo di attacco di forza bruta all'interfaccia SSH.
|**Attività di rete RDP in ingresso sospetta da più origini**|L'analisi del traffico di rete campionata ha rilevato una comunicazione RDP in ingresso anomala da più origini a una risorsa nella distribuzione. Diversi indirizzi IP univoci che si connettono alla risorsa sono considerati anomali per questo ambiente. Questa attività potrebbe indicare un tentativo di attacco di forza bruta all'interfaccia RDP da più host (botnet).|
|**Attività di rete RDP in ingresso sospetta**|L'analisi del traffico di rete campionata ha rilevato una comunicazione RDP in ingresso anomala a una risorsa nella distribuzione. Un numero relativamente elevato di connessioni in ingresso alla risorsa viene considerato anomalo per questo ambiente. Questa attività potrebbe indicare un tentativo di attacco di forza bruta all'interfaccia SSH.|
|**È stata rilevata la comunicazione di rete con un indirizzo dannoso**|L'analisi del traffico di rete campionata ha rilevato la comunicazione originata da una risorsa nella distribuzione con un possibile server di comando e controllo (C & C). Questo tipo di attività potrebbe causare la segnalazione dell'indirizzo IP come dannoso da entità esterne.|

Per comprendere in che modo il Centro sicurezza può usare i segnali correlati alla rete per applicare la protezione dalle minacce, vedere [rilevamento di DNS euristici nel centro sicurezza di Azure](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).

>[!NOTE]
>Gli avvisi di rilevamento delle minacce a livello di rete di Azure, nel centro sicurezza di Azure, vengono generati solo nelle macchine virtuali a cui è stato assegnato lo stesso indirizzo IP per l'intera ora durante la quale si è verificata una comunicazione sospetta. Questo vale per le macchine virtuali, nonché per le macchine virtuali create nella sottoscrizione del cliente come parte di un servizio gestito (ad esempio, AKS, databricks).

## Livello di gestione di Azure (Azure Resource Manager) (anteprima)<a name ="management-layer"></a>

>[!NOTE]
>Il livello di protezione del Centro sicurezza basato su Azure Resource Manager è attualmente in fase di anteprima.

Il Centro sicurezza offre un ulteriore livello di protezione usando gli eventi Azure Resource Manager, che è considerato il piano di controllo per Azure. Analizzando i record di Azure Resource Manager, il Centro sicurezza rileva operazioni insolite o potenzialmente dannose nell'ambiente della sottoscrizione di Azure.

> [!div class="mx-tableFixed"]

|Avviso|DESCRIZIONE|
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

## Azure Key Vault<a name="azure-keyvault"></a>

Azure Key Vault è un servizio cloud che protegge le chiavi di crittografia e i segreti, come certificati, stringhe di connessione e password. 

Il Centro sicurezza di Azure include la protezione avanzata dalle minacce di Azure nativa per Azure Key Vault, offrendo un ulteriore livello di intelligence per la sicurezza. Il Centro sicurezza rileva tentativi insoliti e potenzialmente dannosi di accesso o exploit Key Vault account. Questo livello di protezione consente di risolvere le minacce senza essere un esperto di sicurezza e senza la necessità di gestire sistemi di monitoraggio della sicurezza di terze parti.  

Quando si verificano attività anomale, il Centro sicurezza Mostra gli avvisi e, facoltativamente, li invia tramite posta elettronica agli amministratori della sottoscrizione. Questi avvisi includono i dettagli dell'attività sospetta e consigli su come analizzare e correggere le minacce. 

> [!NOTE]
> Questo servizio non è attualmente disponibile in Azure per enti pubblici e aree cloud sovrane.

> [!div class="mx-tableFixed"]

|Avviso|DESCRIZIONE|
|---|---|
|**Accesso da un nodo di uscita TOR a un Key Vault**|Il Key Vault è stato accessibile da un utente che usa il sistema TOR IP anonimato dei per nasconderne la posizione. Gli attori malintenzionati provano spesso a nascondere la loro posizione quando tentano di ottenere accesso non autorizzato alle risorse connesse a Internet.|
|**Modifica dei criteri sospetti e query segrete in un Key Vault**|È stata apportata una modifica ai criteri di Key Vault, quindi le operazioni per elencare e/o ottenere segreti si sono verificati. Inoltre, questo modello di operazione non viene in genere eseguito dall'utente in questo insieme di credenziali. Si tratta di un'indicazione estremamente indicativa del fatto che il Key Vault è compromesso e che i segreti in sono stati rubati da un attore malintenzionato.|
|**Elenco e query segrete sospette in un Key Vault**|Un'operazione di elenco dei segreti è stata seguita da molte operazioni get segrete. Questo modello di operazione, inoltre, non viene in genere eseguito dall'utente in questo insieme di credenziali. Ciò indica che è possibile che un utente abbia eseguito il dump dei segreti archiviati nel Key Vault per scopi potenzialmente dannosi.|
|**Utente insolito-la coppia di applicazioni ha eseguito l'accesso a un Key Vault**|È stato eseguito l'accesso al Key Vault da parte di un'applicazione utente che in genere non vi accede. Potrebbe trattarsi di un tentativo di accesso legittimo, ad esempio in seguito a un'infrastruttura o a un aggiornamento del codice. Si tratta anche di una possibile indicazione che l'infrastruttura è stata compromessa e che un attore malintenzionato sta tentando di accedere al Key Vault.|
|**Applicazione insolita a cui si accede una Key Vault**|L'accesso a Key Vault è stato eseguito da un'applicazione che normalmente non vi accede. Potrebbe trattarsi di un tentativo di accesso legittimo, ad esempio in seguito a un'infrastruttura o a un aggiornamento del codice. Si tratta anche di una possibile indicazione che l'infrastruttura è stata compromessa e che un attore malintenzionato sta tentando di accedere al Key Vault.|
|**Utente insolito a cui è stato effettuato l'accesso a Key Vault**|È stato eseguito l'accesso al Key Vault da parte di un utente che normalmente non vi accede. Potrebbe trattarsi di un tentativo di accesso legittimo (ad esempio, un nuovo utente che necessita di accesso è stato aggiunto all'organizzazione). Si tratta anche di una possibile indicazione che l'infrastruttura è stata compromessa e che un attore malintenzionato sta tentando di accedere al Key Vault.|
|**Modello di operazione insolito in un Key Vault**|È stato eseguito un set insolito di operazioni di Key Vault rispetto ai dati cronologici. Key Vault attività è in genere la stessa nel tempo. Potrebbe trattarsi di una modifica legittima dell'attività. In alternativa, è possibile che l'infrastruttura venga compromessa e che siano necessarie ulteriori indagini.|
|**Volume elevato di operazioni in una Key Vault**|È stato eseguito un volume maggiore di operazioni di Key Vault rispetto ai dati cronologici. Key Vault attività è in genere la stessa nel tempo. Potrebbe trattarsi di una modifica legittima dell'attività. In alternativa, è possibile che l'infrastruttura venga compromessa e che siano necessarie ulteriori indagini.|
|**Volume elevato di insiemi di credenziali delle chiavi accessibile dall'utente**|Il numero di insiemi di credenziali a cui un utente o un'applicazione accede è stato modificato rispetto ai dati cronologici. Key Vault attività è in genere la stessa nel tempo. Potrebbe trattarsi di una modifica legittima dell'attività. In alternativa, è possibile che l'infrastruttura venga compromessa e che siano necessarie ulteriori indagini.|