---
title: Risolvere i problemi di distribuzione e individuazione di Azure Migrate Appliance
description: Ottenere assistenza per la distribuzione di Azure Migrate Appliance e l'individuazione di computer.
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 854d98a4f2a1b2500bc301d2305c0dd1f27aaaa4
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980824"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>Risolvere i problemi relativi a Azure Migrate Appliance e all'individuazione

Questo articolo illustra come risolvere i problemi durante la distribuzione del dispositivo [Azure migrate](migrate-services-overview.md) e l'uso dell'appliance per individuare i computer locali.


## <a name="whats-supported"></a>Attività supportate

[Esaminare](migrate-appliance.md) i requisiti di supporto dell'appliance.


## <a name="invalid-ovf-manifest-entry"></a>"Voce del manifesto OVF non valida"

Se viene visualizzato l'errore "il file manifesto specificato non è valido: voce del manifesto OVF non valida", eseguire le operazioni seguenti:

1. Verificare che il file OVA del dispositivo Azure Migrate venga scaricato correttamente controllando il relativo valore hash. [Altre informazioni](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware) Se il valore hash non corrisponde, scaricare di nuovo il file OVA e riprovare la distribuzione.
2. Se la distribuzione ha ancora esito negativo e si usa il client VMware vSphere per distribuire il file OVF, provare a distribuirlo tramite il client Web di vSphere. Se la distribuzione non riesce ancora, provare a usare un altro Web browser.
3. Se si usa il client Web vSphere e si prova a distribuirlo in server vCenter 6,5 o 6,7, provare a distribuire gli OVA direttamente nell'host ESXi:
   - Connettersi direttamente all'host ESXi (invece di server vCenter) con il client Web (https://<*indirizzo IP host*>/UI).
   - In **Home** > **Inventory**selezionare **file** > **deploy OVF template**. Passare a OVA e completare la distribuzione.
4. Se l'errore di distribuzione persiste, contattare il supporto di Azure Migrate.

## <a name="cant-connect-to-the-internet"></a>Non è possibile connettersi a Internet

Questo problema può verificarsi se il computer dell'appliance si trova dietro un proxy.

- Assicurarsi di specificare le credenziali di autorizzazione se sono necessarie per il proxy.
- Se si usa un proxy firewall basato su URL per controllare la connettività in uscita, aggiungere questi URL a un elenco Consenti:

    - [URL per la valutazione VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
    - [URL per la valutazione di Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
    - [URL per la migrazione senza agenti VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
    - [URL per la migrazione basata su agenti VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
    - [URL per la migrazione di Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

- Se si usa un proxy di intercettazione per connettersi a Internet, importare il certificato proxy nella macchina virtuale dell'appliance seguendo [questa procedura](https://docs.microsoft.com/azure/migrate/concepts-collector).

##  <a name="datetime-synchronization-error"></a>Errore di sincronizzazione di data/ora

Un errore relativo alla sincronizzazione di data e ora (802) indica che l'orologio del server potrebbe non essere sincronizzato con l'ora corrente da più di cinque minuti. Modificare l'ora di clock della macchina virtuale dell'agente di raccolta in modo che corrisponda all'ora corrente:

1. Nella macchina virtuale aprire un prompt dei comandi come amministratore.
2. Per controllare il fuso orario, eseguire **w32tm/TZ**.
3. Per sincronizzare l'ora, eseguire **w32tm sull'/Resync**.


## <a name="unabletoconnecttoserver"></a>UnableToConnectToServer

Se viene ricevuto questo errore di connessione, potrebbe non essere possibile connettersi a server vCenter *nomeserver*. com: 9443. I dettagli dell'errore indicano che non esiste alcun endpoint in ascolto in https://*ServerName*. com: 9443/SDK che può accettare il messaggio.

- Controllare se si sta eseguendo la versione più recente dell'appliance. In caso contrario, aggiornare l'appliance alla [versione più recente](https://docs.microsoft.com/azure/migrate/concepts-collector).
- Se il problema si verifica ancora nella versione più recente, l'appliance potrebbe non essere in grado di risolvere il nome del server vCenter specificato oppure la porta specificata potrebbe essere errata. Per impostazione predefinita, se la porta non è specificata, l'agente di raccolta tenterà di connettersi al numero di porta 443.

    1. Eseguire il ping *ServerName*. com dall'appliance.
    2. Se il passaggio 1 ha esito negativo, provare a connettersi al server vCenter usando l'indirizzo IP.
    3. Identificare il numero di porta corretto per la connessione server vCenter.
    4. Verificare che server vCenter sia attivo e in esecuzione.


## <a name="error-6005260039-appliance-might-not-be-registered"></a>Errore 60052/60039: l'appliance potrebbe non essere registrata

- Errore 60052, "l'appliance potrebbe non essere stata registrata correttamente nel progetto Azure Migrate" si verifica se l'account Azure usato per registrare l'appliance non dispone di autorizzazioni sufficienti.
    - Assicurarsi che l'account utente di Azure usato per registrare il dispositivo disponga almeno delle autorizzazioni di collaboratore per la sottoscrizione.
    - [Altre](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) informazioni sui ruoli e sulle autorizzazioni di Azure richiesti.
- Errore 60039: "è possibile che l'appliance non sia stata registrata correttamente nel progetto Azure Migrate" se la registrazione non riesce perché il progetto Azure Migrate usato per registrare l'appliance non è stato trovato.
    - Nel portale di Azure e verificare se il progetto esiste nel gruppo di risorse.
    - Se il progetto non esiste, creare un nuovo progetto di Azure Migrate nel gruppo di risorse e registrare di nuovo l'appliance. [Informazioni su come](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) creare un nuovo progetto.

## <a name="error-6003060031-key-vault-management-operation-failed"></a>Errore 60030/60031: operazione di gestione Key Vault non riuscita

Se viene visualizzato l'errore 60030 o 60031, "un'operazione di gestione Azure Key Vault non riuscita", eseguire le operazioni seguenti:
- Assicurarsi che l'account utente di Azure usato per registrare il dispositivo disponga almeno delle autorizzazioni di collaboratore per la sottoscrizione.
- Verificare che l'account abbia accesso all'insieme di credenziali delle chiavi specificato nel messaggio di errore, quindi ripetere l'operazione.
- Se il problema persiste, contattare il supporto tecnico Microsoft.
- [Altre](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) informazioni sui ruoli e sulle autorizzazioni di Azure richiesti.

## <a name="error-60028-discovery-couldnt-be-initiated"></a>Errore 60028: non è stato possibile avviare l'individuazione

Errore 60028: "Impossibile avviare l'individuazione a causa di un errore. L'operazione non è riuscita per l'elenco specificato di host o cluster "indica che non è stato possibile avviare l'individuazione negli host elencati nell'errore a causa di un problema durante l'accesso o il recupero delle informazioni sulla macchina virtuale. Il resto degli host è stato aggiunto correttamente.

- Aggiungere di nuovo gli host elencati nell'errore utilizzando l'opzione **Aggiungi host** .
- Se si verifica un errore di convalida, rivedere le indicazioni per la correzione per correggere gli errori, quindi provare a eseguire di nuovo l'opzione **Salva e avvia individuazione** .

## <a name="error-60025-azure-ad-operation-failed"></a>Errore 60025: operazione Azure AD non riuscita 
Errore 60025: "un'operazione Azure AD non è riuscita. L'errore si è verificato durante la creazione o l'aggiornamento dell'applicazione Azure AD "si verifica quando l'account utente di Azure usato per avviare l'individuazione è diverso dall'account usato per registrare l'appliance. Eseguire una delle operazioni seguenti:

- Assicurarsi che l'account utente che avvia l'individuazione sia uguale a quello usato per registrare l'appliance.
- Fornire Azure Active Directory le autorizzazioni di accesso alle applicazioni per l'account utente per il quale l'operazione di individuazione ha esito negativo.
- Eliminare il gruppo di risorse creato in precedenza per il progetto Azure Migrate. Creare un altro gruppo di risorse per riavviarlo.
- [Altre](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) informazioni sulle autorizzazioni per l'applicazione Azure Active Directory.


## <a name="error-50004-cant-connect-to-host-or-cluster"></a>Errore 50004: non è possibile connettersi all'host o al cluster

Errore 50004: "Impossibile connettersi a un host o a un cluster perché il nome del server non può essere risolto. Codice errore WinRM: 0x803381B9 "potrebbe verificarsi se il servizio DNS di Azure per l'appliance non riesce a risolvere il cluster o il nome host specificato.

- Se viene visualizzato questo errore nel cluster, FQDN del cluster.
- Questo errore può essere visualizzato anche per gli host in un cluster. Ciò indica che l'appliance è in grado di connettersi al cluster, ma il cluster restituisce nomi host che non sono FQDN. Per correggere l'errore, aggiornare il file hosts nell'appliance aggiungendo un mapping dell'indirizzo IP e dei nomi host:
    1. Aprire Blocco note come amministratore.
    2. Aprire il file C:\Windows\System32\Drivers\etc\hosts.
    3. Aggiungere l'indirizzo IP e il nome host in una riga. Ripetere l'operazione per ogni host o cluster in cui viene visualizzato questo errore.
    4. Salvare e chiudere il file degli host.
    5. Controllare se l'appliance è in grado di connettersi agli host usando l'app di gestione Appliance. Dopo 30 minuti, nel portale di Azure verranno visualizzate le informazioni più recenti per questi host.

## <a name="discovered-vms-not-in-portal"></a>Macchine virtuali individuate non nel portale

Se lo stato di individuazione è "individuazione in corso", ma non sono ancora visibili le VM nel portale, attendere alcuni minuti:
- Sono necessari circa 15 minuti per una VM VMware.
- Sono necessari circa due minuti per ogni host aggiunto per l'individuazione di macchine virtuali Hyper-V.

Se si attende che lo stato non cambia, selezionare **Aggiorna** nella scheda **Server** . Verrà visualizzato il numero dei server individuati in Azure Migrate: server assessment e Azure Migrate: migrazione server.

Se questa operazione non funziona e si stanno scoprendo i server VMware:

- Verificare che l'account vCenter specificato disponga delle autorizzazioni impostate correttamente, con accesso ad almeno una macchina virtuale.
- Azure Migrate non riesce a individuare le macchine virtuali VMware se l'account vCenter ha accesso concesso a livello di cartella della macchina virtuale vCenter. [Altre](tutorial-assess-vmware.md#set-the-scope-of-discovery) informazioni sull'individuazione dell'ambito.

## <a name="vm-data-not-in-portal"></a>Dati della macchina virtuale non nel portale

Se le macchine virtuali individuate non vengono visualizzate nel portale, attendere alcuni minuti. Per visualizzare i dati individuati nel portale sono necessari fino a 30 minuti. Se non sono presenti dati dopo 30 minuti, provare ad aggiornare, come indicato di seguito.

1. In **server** > **Azure migrate server Assessment**Selezionare **Panoramica**.
2. In **Gestisci**selezionare **integrità agente**.
3. Selezionare **Aggiorna agente**.
4. Attendere il completamento dell'operazione di aggiornamento. Verranno ora visualizzate informazioni aggiornate.

## <a name="deleted-vms-appear-in-portal"></a>Le VM eliminate vengono visualizzate nel portale

Se si eliminano macchine virtuali che vengono comunque visualizzate nel portale, attendere 30 minuti. Se vengono ancora visualizzati, aggiornare come descritto in precedenza.

## <a name="common-app-discovery-errors"></a>Errori comuni di individuazione delle app

Azure Migrate supporta l'individuazione di applicazioni, ruoli e funzionalità, utilizzando Azure Migrate: server assessment. L'individuazione delle app è attualmente supportata solo per VMware. [Altre](how-to-discover-applications.md) informazioni sui requisiti e i passaggi per la configurazione dell'individuazione delle app.

I tipici errori di individuazione delle app sono riepilogati nella tabella.

**Error (Errore) (Error (Errore)e)** | **Causa** | **Azione**
--- | --- | --- | ---
10000: "Impossibile individuare le applicazioni installate nel server". | Questo problema può verificarsi se il sistema operativo del computer non è Windows o Linux. | Usare solo l'individuazione delle app per Windows/Linux.
10001: "Impossibile recuperare le applicazioni installate nel server". | Errore interno-alcuni file mancanti nell'appliance. | Contattare il supporto Microsoft.
10002: "Impossibile recuperare le applicazioni installate nel server". | L'agente di individuazione nell'appliance potrebbe non funzionare correttamente. | Se il problema non si risolve automaticamente entro 24 ore, contattare il supporto tecnico.
10003 "Impossibile recuperare le applicazioni installate nel server". | L'agente di individuazione nell'appliance potrebbe non funzionare correttamente. | Se il problema non si risolve automaticamente entro 24 ore, contattare il supporto tecnico.
10004: "Impossibile individuare le applicazioni installate per < macchine > Windows/Linux". |  Le credenziali per l'accesso < macchine > Windows/Linux non sono state fornite nell'appliance.| Aggiungere una credenziale al dispositivo che ha accesso ai computer > < Windows/Linux.
10005: "Impossibile accedere al server locale". | Le credenziali di accesso potrebbero essere errate. | Aggiornare le credenziali dell'appliance per assicurarsi di poter accedere al computer pertinente. 
10006: "Impossibile accedere al server locale". | Questo problema può verificarsi se il sistema operativo del computer non è Windows o Linux.|  Usare solo l'individuazione delle app per Windows/Linux.
9000/9001/9002: "Impossibile individuare le applicazioni installate nel server". | È possibile che gli strumenti VMware non siano installati o siano danneggiati. | Installare o reinstallare gli strumenti VMware nel computer pertinente e verificare che sia in esecuzione.
9003: Impossibile individuare le applicazioni installate nel server ". | Questo problema può verificarsi se il sistema operativo del computer non è Windows o Linux. | Usare solo l'individuazione delle app per Windows/Linux.
9004: "Impossibile individuare le applicazioni installate nel server". | Questo problema può verificarsi se la macchina virtuale è spenta. | Per l'individuazione, verificare che la macchina virtuale sia attiva.
9005: "Impossibile individuare le applicazioni installate nella macchina virtuale. | Questo problema può verificarsi se il sistema operativo del computer non è Windows o Linux. | Usare solo l'individuazione delle app per Windows/Linux.
9006/9007: "Impossibile recuperare le applicazioni installate nel server". | L'agente di individuazione nell'appliance potrebbe non funzionare correttamente. | Se il problema non si risolve automaticamente entro 24 ore, contattare il supporto tecnico.
9008: "Impossibile recuperare le applicazioni installate nel server". | Potrebbe trattarsi di un errore interno.  | TF il problema non si risolve automaticamente entro 24 ore, contattare il supporto tecnico.
9009: "Impossibile recuperare le applicazioni installate nel server". | Può verificarsi se le impostazioni di controllo dell'account utente di Windows nel server sono restrittive e impedire l'individuazione delle applicazioni installate. | Cercare le impostazioni di controllo dell'account utente nel server e configurare l'impostazione UAC sul server su uno dei due livelli inferiori.
9010: "Impossibile recuperare le applicazioni installate nel server". | Potrebbe trattarsi di un errore interno.  | TF il problema non si risolve automaticamente entro 24 ore, contattare il supporto tecnico.
8084: "Impossibile individuare le applicazioni a causa di un errore VMware: <Exception from VMware>" | Il Azure Migrate Appliance usa le API VMware per individuare le applicazioni. Questo problema può verificarsi se un'eccezione viene generata da server vCenter durante il tentativo di individuazione delle applicazioni. Il messaggio di errore di VMware viene visualizzato nel messaggio di errore visualizzato nel portale. | Cercare il messaggio nella documentazione di [VMware](https://pubs.vmware.com/vsphere-51/topic/com.vmware.wssdk.apiref.doc/index-faults.html)e seguire i passaggi da eseguire per risolvere il problema. Se non è possibile risolvere il problema, contattare il supporto tecnico Microsoft.




## <a name="next-steps"></a>Passaggi successivi
Configurare un'appliance per [VMware](how-to-set-up-appliance-vmware.md), [Hyper-V](how-to-set-up-appliance-hyper-v.md)o [server fisici](how-to-set-up-appliance-physical.md).
