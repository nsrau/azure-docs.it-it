---
title: "Disponibilità elevata con il gateway di gestione dati in Azure Data Factory | Microsoft Docs"
description: "Questo articolo illustra come è possibile aumentare il numero di istanze di un gateway di gestione dati aggiungendo altri nodi e aumentare le prestazioni accrescendo il numero di processi simultanei che possono essere eseguiti in un nodo."
services: data-factory
documentationcenter: 
author: nabhishek
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: abnarain
robots: noindex
ms.openlocfilehash: fda3c7a9a369eec1b9033ee7077a5f3770647c9f
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2017
---
# <a name="data-management-gateway---high-availability-and-scalability-preview"></a>Gateway di gestione dati: disponibilità elevata e scalabilità (anteprima)
> [!NOTE]
> Questo articolo si applica alla versione 1 del servizio Data Factory, disponibile a livello generale (GA). Se si usa la versione 2 del servizio Data Factory, disponibile in anteprima, vedere le informazioni sul [runtime di integrazione self-hosted nella versione 2](../create-self-hosted-integration-runtime.md). 


Questo articolo consente di configurare una soluzione di disponibilità elevata e scalabilità con Gateway di gestione dati/Integration Runtime.    

> [!NOTE]
> Questo articolo presuppone che l'utente abbia già familiarità con i concetti di base relativi a Integration Runtime (in precedenza, Gateway di gestione dati). In caso contrario, vedere [Gateway di gestione dati](data-factory-data-management-gateway.md).

>**Questa funzionalità di anteprima è ufficialmente supportata in Gateway di gestione dati versione 2.12.xxxx.x e successive**. Assicurarsi quindi che sia in uso la versione 2.12.xxxx.x o successiva. Scaricare la versione più recente di Gateway di gestione dati [qui](https://www.microsoft.com/download/details.aspx?id=39717).

## <a name="overview"></a>Panoramica
È possibile associare gateway di gestione dati installati in più computer locali a un solo gateway logico nel portale. Questi computer sono chiamati **nodi**. È possibile avere fino a **quattro nodi** associati a un gateway logico. I vantaggi di avere più nodi (computer locali con un gateway installato) per un gateway logico sono:  

- Migliorare le prestazioni dello spostamento dati tra archivi dati locali e cloud.  
- Se uno dei nodi diventa inattivo per qualsiasi motivo, gli altri nodi rimangono disponibili per lo spostamento dei dati. 
- Se uno dei nodi deve essere portato offline per la manutenzione, gli altri nodi rimangono disponibili per lo spostamento dei dati.

È anche possibile configurare il numero di **processi di spostamento dati simultanei** che possono essere eseguiti in un nodo per aumentare le prestazioni dello spostamento dei dati tra archivi dati locali e cloud. 

Usando il portale di Azure, è possibile monitorare lo stato di questi nodi, per poter decidere se aggiungere o rimuovere un nodo dal gateway logico. 

## <a name="architecture"></a>Architettura 
Il diagramma seguente offre una panoramica dell'architettura della funzionalità di scalabilità e disponibilità del gateway di gestione dati: 

![Gateway di gestione dati: disponibilità elevata e scalabilità](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-high-availability-and-scalability.png)

Un **gateway logico** è il gateway che si aggiunge a una data factory nel portale di Azure. Prima era possibile associare a un gateway logico un solo computer Windows locale con Gateway di gestione dati installato. Questo computer gateway locale è detto nodo. Ora è possibile associare fino a **quattro nodi fisici** a un gateway logico. Un gateway logico con più nodi è detto **gateway multinodo**.  

Tutti questi nodi sono **attivi**. Tutti possono elaborare i processi di spostamento dati per spostare i dati tra gli archivi dati locali e cloud. Uno dei nodi funge sia da dispatcher che da ruolo di lavoro. Gli altri nodi dei gruppi sono nodi ruolo di lavoro. Un nodo **dispatcher** esegue il pull di attività/processi di spostamento dati dal servizio cloud e li invia ai nodi ruolo di lavoro (incluso se stesso). Un nodo **ruolo di lavoro** esegue processi di spostamento dati per spostare i dati tra gli archivi dati locali e cloud. Tutti i nodi sono ruoli di lavoro. Solo un nodo può essere sia dispatcher che ruolo di lavoro.    

È in genere possibile iniziare con un nodo e **aumentare il numero di istanze** per aggiungere altri nodi man mano che i nodi esistenti vengono sovraccaricati dalle operazioni di spostamento dati. È anche possibile **aumentare le prestazioni** della funzionalità di spostamento dati di un nodo del gateway accrescendo il numero di processi simultanei che possono essere eseguiti nel nodo. Questa capacità è disponibile anche con un gateway a nodo singolo, anche quando la funzionalità di scalabilità e disponibilità non è abilitata. 

Un gateway con più nodi mantiene sincronizzate le credenziali degli archivi dati in tutti i nodi. Se si verifica un problema di connettività tra nodi, le credenziali potrebbero non essere sincronizzate. Quando si impostano le credenziali per un archivio dati locale che usa un gateway, le credenziali vengono salvate nel nodo dispatcher/ruolo di lavoro. Il nodo dispatcher viene sincronizzato con gli altri nodi ruolo di lavoro. Questo processo è noto come **sincronizzazione delle credenziali**. Il canale di comunicazione tra i nodi può essere **crittografato** da un certificato SSL/TLS pubblico. 

## <a name="set-up-a-multi-node-gateway"></a>Configurare un gateway multinodo
Questa sezione presuppone che siano stati letti i due articoli seguenti o che si abbia familiarità con i concetti illustrati in questi articoli: 

- [Gateway di gestione dati](data-factory-data-management-gateway.md): offre una panoramica dettagliata del gateway.
- [Spostare dati tra archivi dati locali e cloud](data-factory-move-data-between-onprem-and-cloud.md): contiene una procedura dettagliata con le istruzioni per usare un gateway a nodo singolo.  

> [!NOTE]
> Prima di installare un gateway di gestione dati in un computer Windows locale, vedere i prerequisiti elencati nell'[articolo principale](data-factory-data-management-gateway.md#prerequisites).

1. Nella [procedura dettagliata](data-factory-move-data-between-onprem-and-cloud.md#create-gateway), durante la creazione di un gateway logico, abilitare la funzionalità **Disponibilità elevata e scalabilità**. 

    ![Gateway di gestione dati: abilitare disponibilità elevata e scalabilità](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-enable-high-availability-scalability.png)
2. Nella pagina **Configura** usare il collegamento **Installazione rapida** o **Installazione manuale** per installare un gateway nel primo nodo (un computer Windows locale).

    ![Gateway di gestione dati: installazione rapida o manuale](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-express-manual-setup.png)

    > [!NOTE]
    > Se si usa l'opzione Installazione rapida, la comunicazione tra nodi viene eseguita senza crittografia. Il nome del nodo è uguale al nome del computer. Usare l'installazione manuale se la comunicazione tra nodi deve essere crittografata o per specificare un nome di nodo di propria scelta. I nomi di nodo non possono essere modificati in un secondo momento.
3. Se si sceglie **Installazione rapida**
    1. Al termine dell'installazione del gateway, viene visualizzato il messaggio seguente:

        ![Gateway di gestione dati: installazione rapida completata](media/data-factory-data-management-gateway-high-availability-scalability/express-setup-success.png)
    2. Avviare Gestione configurazione di Gateway di gestione dati per il gateway seguendo [queste istruzioni](data-factory-data-management-gateway.md#configuration-manager). Vengono visualizzati il nome del gateway, il nome del nodo, lo stato e così via.

        ![Gateway di gestione dati: installazione completata](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)
4. Se si sceglie **Installazione manuale**:
    1. Scaricare il pacchetto di installazione dall'Area download Microsoft ed eseguirlo per installare il gateway nel computer.
    2. Usare la **chiave di autenticazione** dalla pagina **Configura** per registrare il gateway.
    
        ![Gateway di gestione dati: installazione completata](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-authentication-key.png)
    3. Nella pagina **Nuovo nodo del gateway** è possibile specificare un **nome** personalizzato per il nodo del gateway. Per impostazione predefinita, il nome del nodo è uguale al nome del computer.    

        ![Gateway di gestione dati: specificare un nome](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-name.png)
    4. Nella pagina successiva è possibile scegliere se **abilitare la crittografia per la comunicazione tra nodi**. Fare clic su **Ignora** per disabilitare la crittografia (impostazione predefinita).

        ![Gateway di gestione dati: abilitare la crittografia](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-node-encryption.png)  
    
        > [!NOTE]
        > La modifica della modalità di crittografia è supportata solo quando si ha un nodo del gateway singolo nel gateway logico. Per modificare la modalità di crittografia, quando un gateway ha più nodi, seguire questa procedura: eliminare tutti i nodi tranne uno, modificare la modalità di crittografia e quindi aggiungere di nuovo i nodi.
        > 
        > Per un elenco dei requisiti per l'uso di un certificato TLS/SSL, vedere la sezione [Requisiti dei certificati TLS/SSL](#tlsssl-certificate-requirements). 
    5. Al termine dell'installazione del gateway, fare clic su Avvia Configuration Manager:
    
        ![Installazione manuale: avviare Gestione configurazione](media/data-factory-data-management-gateway-high-availability-scalability/manual-setup-launch-configuration-manager.png)   
    6. Nel nodo viene visualizzato Gestione configurazione di Gateway di gestione dati (computer Windows locale), che indica lo stato della connettività, il **nome del gateway** e il **nome del nodo**.  

        ![Gateway di gestione dati: installazione completata](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)

        > [!NOTE]
        > Se si sta effettuando il provisioning del gateway in una macchina virtuale di Azure, è possibile usare [questo modello di Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-mutiple-vms-with-data-management-gateway). Questo script crea un gateway di rete logico, configura le VM con il software di Gateway di gestione dati installato e le registra con il gateway logico. 
6. Nel portale di Azure avviare la pagina **Gateway**: 
    1. Nella home page della data factory nel portale fare clic su **Servizi collegati**.
    
        ![Home page di Data factory](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-home-page.png)
    2. Selezionare il **gateway** per visualizzare la pagina **Gateway**:
    
        ![Home page di Data factory](media/data-factory-data-management-gateway-high-availability-scalability/linked-services-gateway.png)
    4. Viene visualizzata la pagina **Gateway**:   

        ![Visualizzazione del gateway con un nodo singolo](media/data-factory-data-management-gateway-high-availability-scalability/gateway-first-node-portal-view.png) 
7. Fare clic su **Aggiungi nodo** sulla barra degli strumenti per aggiungere un nodo al gateway logico. Se si prevede di usare l'installazione rapida, eseguire questo passaggio dal computer locale che verrà aggiunto come nodo al gateway. 

    ![Gateway di gestione dati: menu Aggiungi nodo](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)
8. La procedura è simile alla configurazione del primo nodo. L'interfaccia utente di Gestione configurazione consente di impostare il nome del nodo se si sceglie l'opzione di installazione manuale: 

    ![Gestione configurazione: installare il secondo gateway](media/data-factory-data-management-gateway-high-availability-scalability/install-second-gateway.png)
9. Al termine dell'installazione del gateway nel nodo, lo strumento Gestione configurazione visualizza la schermata seguente:  

    ![Gestione configurazione: installare il secondo gateway](media/data-factory-data-management-gateway-high-availability-scalability/second-gateway-installation-successful.png)
10. Se si apre la pagina **Gateway** nel portale, ora vengono visualizzati i due nodi del gateway: 

    ![Gateway con due nodi nel portale](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)
11. Per eliminare un nodo del gateway, fare clic su **Elimina nodo** sulla barra degli strumenti, selezionare il nodo che si vuole eliminare e quindi fare clic su **Elimina** sulla barra degli strumenti. Questa azione elimina il nodo selezionato dal gruppo. Si noti che questa azione non disinstalla il software del gateway di gestione dati dal nodo (computer Windows locale). Usare **Installazione applicazioni** nel Pannello di controllo nel computer locale per disinstallare il gateway. Quando il nodo viene disinstallato dal gateway, viene automaticamente eliminato nel portale.   

## <a name="upgrade-an-existing-gateway"></a>Aggiornare un gateway esistente
È possibile aggiornare un gateway esistente per usare la funzionalità di disponibilità elevata e scalabilità. Questa funzionalità può essere usata solo con i nodi che eseguono Gateway di gestione dati versione >= 2.12.xxxx. È possibile visualizzare la versione del gateway di gestione dati installato in un computer nella scheda **Guida** di Gestione configurazione di Gateway di gestione dati. 

1. Aggiornare il gateway nel computer locale alla versione più recente scaricando ed eseguendo un pacchetto di installazione MSI dall'[Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). Vedere la sezione [Installazione](data-factory-data-management-gateway.md#installation) per i dettagli.  
2. Passare al portale di Azure. Avviare la **pagina Data Factory** per la data factory. Fare clic sul riquadro Servizi collegati per avviare la **pagina Servizi collegati**. Selezionare il gateway per avviare la **pagina del gateway**. Facendo clic abilitare **Anteprima funzionalità**, come mostrato nell'immagine seguente: 

    ![Gateway di gestione dati: abilitare l'anteprima funzionalità](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-existing-gateway-enable-high-availability.png)   
2. Dopo avere abilitato l'anteprima funzionalità nel portale, chiudere tutte le pagine. Riaprire la **pagina del gateway** per visualizzare la nuova interfaccia utente di anteprima.
 
    ![Gateway di gestione dati: abilitazione dell'anteprima funzionalità riuscita](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview-success.png)

    ![Gateway di gestione dati: interfaccia utente di anteprima](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview.png)

    > [!NOTE]
    > Durante l'aggiornamento, il nome del primo nodo è il nome del computer. 
3. Aggiungere ora un nodo. Nella pagina **Gateway** fare clic su **Aggiungi nodo**.  

    ![Gateway di gestione dati: menu Aggiungi nodo](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)

    Seguire le istruzioni della sezione precedente per configurare il nodo. 

### <a name="installation-best-practices"></a>Procedure consigliate per l'installazione

- Configurare la combinazione per il risparmio di energia nel computer host del gateway in modo che il computer non entri in stato di ibernazione. Se il computer host entra in stato di ibernazione, il gateway non risponde alle richieste di dati.
- Eseguire il backup del certificato associato al gateway.
- Per prestazioni ottimali, assicurarsi che tutti i nodi abbiano un configurazione simile (consigliato). 
- Aggiungere almeno due nodi per assicurare la disponibilità elevata.  

### <a name="tlsssl-certificate-requirements"></a>Requisiti del certificato TLS/SSL
Ecco i requisiti per il certificato TLS/SSL usato per proteggere le comunicazioni tra i nodi di Integration Runtime:

- Deve essere un certificato X509 v3 pubblicamente attendibile. È consigliabile usare certificati rilasciati da un'autorità di certificazione (CA) pubblica (terza parte).
- Ogni nodo di Integration Runtime deve considerare attendibile questo certificato, nonché il computer client che esegue l'applicazione di gestione delle credenziali. 
> [!NOTE]
> L'applicazione di gestione delle credenziali viene usata durante l'impostazione sicura delle credenziali tramite Copia guidata nel portale di Azure. Può inoltre essere attivata da qualsiasi computer all'interno della stessa rete dell'archivio dati locale/privato.
- I certificati con caratteri jolly sono supportati. Se il nome FQDN è **node1.domain.contoso.com**, è possibile usare ***.domain.contoso.com** come nome del soggetto del certificato.
- I certificati SAN non sono consigliati poiché verrà usato solo l'ultimo elemento dei nomi alternativi dei soggetti, mentre tutti gli altri verranno ignorati a causa della limitazione attuale. Ad esempio, se si dispone di un certificato SAN i cui nomi alternativi dei soggetti sono **node1.domain.contoso.com** e **node2.domain.contoso.com**, è possibile usare solo questo certificato nel computer il cui nome di dominio completo è **node2.domain.contoso.com**.
- Deve supportare tutte le dimensioni chiave supportate da Windows Server 2012 R2 per i certificati SSL.
- Non sono supportati i certificati che usano chiavi CNG. Non sono supportati i certificati che usano chiavi CNG.

#### <a name="faq-when-would-i-not-enable-this-encryption"></a>Domande frequenti: Quando non è consigliabile abilitare questo tipo di crittografia?
L'abilitazione della crittografia può generare costi a livello di infrastruttura proprietaria del certificato pubblico. È pertanto consigliabile evitare di abilitare la crittografia nei casi seguenti:
- Quando Integration Runtime è in esecuzione in una rete attendibile o in una rete con crittografia trasparente, ad esempio IP/SEC. Poiché questa comunicazione del canale è limitata solo all'interno della rete attendibile, una crittografia aggiuntiva potrebbe non essere necessaria.
- Quando Integration Runtime non è in esecuzione in un ambiente di produzione. Ciò consente di ridurre il costo dei certificati TLS/SSL.


## <a name="monitor-a-multi-node-gateway"></a>Monitorare un gateway multinodo
### <a name="multi-node-gateway-monitoring"></a>Monitoraggio di un gateway multinodo
Nel portale di Azure è possibile visualizzare lo snapshot in tempo quasi reale dell'utilizzo delle risorse, ad esempio CPU, memoria, rete (ingresso/uscita) e così via, in ogni nodo con gli stati dei nodi del gateway. 

![Gateway di gestione dati: monitoraggio di più nodi](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)

È possibile abilitare **Impostazioni avanzate** nella pagina **Gateway** per visualizzare le metriche avanzate, ad esempio **Rete (in/out)**, **Ruolo e Stato delle credenziali**, utile nel debug dei problemi del gateway, e **Processi simultanei (in esecuzione/limite)** che può essere modificata/cambiata in base alle esigenze durante l'ottimizzazione delle prestazioni. La tabella seguente contiene le descrizioni delle colonne dell'elenco **Nodi del gateway**:  

Proprietà monitoraggio | Descrizione
:------------------ | :---------- 
Nome | Nome del gateway logico e nodi associati al gateway.  
Status | Stato del gateway logico e dei nodi del gateway. Esempio: Online/Offline/Limitato e così via. Per informazioni su questi stati, vedere la sezione [Stato del gateway](#gateway-status). 
Versione | Indica la versione del gateway logico e di ogni nodo del gateway. La versione del gateway logico viene determinata in base alla versione della maggior parte dei nodi del gruppo. Se nella configurazione del gateway logico sono presenti nodi con versioni diverse, solo i nodi con lo stesso numero di versione del gateway logico funzionano correttamente. Gli altri sono in modalità limitata e devono essere aggiornati manualmente (solo se l'aggiornamento automatico non riesce). 
Memoria disponibile | Memoria disponibile in un nodo del gateway. Questo valore è uno snapshot in tempo quasi reale. 
Uso della CPU | Utilizzo della CPU di un nodo del gateway. Questo valore è uno snapshot in tempo quasi reale. 
Rete (in/out) | Utilizzo della rete da parte di un nodo del gateway. Questo valore è uno snapshot in tempo quasi reale. 
Processi simultanei (in esecuzione/limite) | Numero di processi o di attività in esecuzione in ogni nodo. Questo valore è uno snapshot in tempo quasi reale. Per limite si intende il numero massimo di processi simultanei per ogni nodo. Questo valore viene definito in base alle dimensioni del computer. È possibile accrescere il limite per aumentare le prestazioni dell'esecuzione dei processi simultanei negli scenari avanzati, in cui CPU/memoria/rete sono sottoutilizzate, ma è in corso il timeout delle attività. Questa capacità è disponibile anche con un gateway a nodo singolo, anche quando la funzionalità di scalabilità e disponibilità non è abilitata. Per altre informazioni, vedere la sezione [Considerazioni sulla scalabilità](#scale-considerations). 
Ruolo | Esistono due tipi di ruoli: dispatcher e ruolo di lavoro. Tutti i nodi sono ruoli di lavoro e quindi possono essere tutti usati per eseguire i processi. Esiste un solo nodo dispatcher, che viene usato per eseguire il pull di attività/processi dai servizi cloud e inviarli a nodi ruolo di lavoro diversi (incluso lo stesso nodo dispatcher). 

![Gateway di gestione dati: monitoraggio avanzato di più nodi](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-advanced.png)

### <a name="gateway-status"></a>Stato del gateway

La tabella seguente indica i possibili stati di un **nodo del gateway**: 

Status  | Commenti/Scenari
:------- | :------------------
Online | Nodo connesso al servizio Data Factory.
Offline | Il nodo è offline.
Aggiornamento | È in corso l'aggiornamento automatico del nodo.
Limitato | La causa è un problema di connettività, ad esempio un problema della porta HTTP 8050, di connettività del bus di servizio o di sincronizzazione delle credenziali. 
Inactive | Il nodo è in una configurazione diversa da quella della maggior parte degli altri nodi.<br/><br/> Un nodo può essere inattivo quando non riesce a connettersi agli altri nodi. 


La tabella seguente indica i possibili stati di un **gateway logico**. Lo stato del gateway dipende dagli stati dei nodi del gateway. 

Status | Commenti
:----- | :-------
Needs Registration (Registrazione necessaria) | Nessun nodo è ancora registrato per questo gateway logico
Online | I nodi del gateway sono online
Offline | Nessun nodo nello stato online.
Limitato | Non tutti i nodi in questo gateway sono in uno stato integro. Questo stato è un avviso indicante che qualche nodo potrebbe essere inattivo. <br/><br/>La causa potrebbe essere un problema di sincronizzazione delle credenziali nel nodo dispatcher/ruolo di lavoro. 

### <a name="pipeline-activities-monitoring"></a>Monitoraggio di pipeline/attività
Il portale di Azure offre un'esperienza di monitoraggio della pipeline con dettagli granulari a livello di nodo, ad esempio mostra le attività eseguite in ogni nodo. Queste informazioni possono essere utili per conoscere i problemi di prestazioni in un particolare nodo, ad esempio a causa della limitazione della larghezza di banda. 

![Gateway di gestione dati: monitoraggio di più nodi per le pipeline](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-pipelines.png)

![Gateway di gestione dati: dettagli della pipeline](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-pipeline-details.png)

## <a name="scale-considerations"></a>Considerazioni sulla scalabilità

### <a name="scale-out"></a>Scalabilità orizzontale
Quando la **memoria disponibile non è sufficiente** e l'**utilizzo della CPU è elevato**, l'aggiunta di un nuovo nodo consente di aumentare il carico nei computer. Se le attività hanno esito negativo perché si verifica un timeout o il nodo del gateway è offline, è utile aggiungere un nodo al gateway.
 
### <a name="scale-up"></a>Aumentare le prestazioni
Quando la memoria disponibile e la CPU non vengono utilizzate correttamente, ma la capacità inattiva è 0, è consigliabile aumentare le prestazioni accrescendo il numero di processi simultanei che possono essere eseguiti in un nodo. È anche possibile aumentare le prestazioni quando si verificano timeout delle attività perché il gateway è sovraccarico. Come illustrato nell'immagine seguente, è possibile aumentare la capacità massima di un nodo. Per iniziare, è consigliabile raddoppiarla.  

![Gateway di gestione dati: considerazioni sulla scalabilità](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-scale-considerations.png)


## <a name="known-issuesbreaking-changes"></a>Problemi noti/modifiche significative

- È attualmente possibile avere fino a quattro nodi fisici per ogni gateway logico. Se sono necessari più di quattro nodi per motivi di prestazioni, inviare un messaggio di posta elettronica a [DMGHelp@microsoft.com](mailto:DMGHelp@microsoft.com).
- Non è possibile ripetere la registrazione di un nodo del gateway con la chiave di autenticazione da un altro gateway logico per eseguire il passaggio dal gateway logico corrente. Per ripetere la registrazione, disinstallare il gateway dal nodo, reinstallare il gateway e registrarlo con la chiave di autenticazione dell'altro gateway logico. 
- Se per tutti i nodi del gateway è necessario il proxy HTTP, impostare il proxy in diahost.exe.config e diawp.exe.config e usare Server Manager per verificare che tutti i nodi abbiano gli stessi file diahost.exe.config e diawip.exe.config. Per informazioni dettagliate, vedere la sezione [Configurare le impostazioni del server proxy](data-factory-data-management-gateway.md#configure-proxy-server-settings). 
- Per modificare la modalità di crittografia per la comunicazione tra nodi in Gateway di gestione dati, eliminare tutti i nodi nel portale tranne uno, quindi aggiungerli di nuovo dopo avere modificato la modalità di crittografia.
- Usare un certificato SSL ufficiale se si sceglie di crittografare il canale di comunicazione tra nodi. Un certificato autofirmato può causare problemi di connettività perché lo stesso certificato potrebbe non essere considerato attendibile nell'elenco delle autorità di certificazione di altri computer. 
- Non è possibile registrare un nodo del gateway in un gateway logico quando la versione del nodo è precedente alla versione del gateway logico. Eliminare tutti i nodi del gateway logico dal portale per poter registrare un nodo con una versione precedente (effettuarne il downgrade). Se si eliminano tutti i nodi di un gateway logico, installare manualmente e registrare i nuovi nodi per tale gateway logico. In questo caso l'installazione rapida non è supportata.
- Non è possibile usare l'installazione rapida per installare i nodi in un gateway logico esistente che usa ancora le credenziali cloud. È possibile controllare se le credenziali vengono archiviate da Gestione configurazione di Gateway di gestione dati nella scheda Impostazioni.
- Non è possibile usare l'installazione rapida per installare i nodi in un gateway logico esistente che ha la crittografia da nodo a nodo abilitata. Poiché l'impostazione della modalità di crittografia comporta l'aggiunta manuale dei certificati, l'installazione rapida non è più possibile. 
- Per copiare i file dall'ambiente locale, non è più consigliabile usare \\localhost o C:\files perché localhost o l'unità locale potrebbe non essere accessibile tramite tutti i nodi. Usare invece \\ServerName\files per specificare il percorso dei file.


## <a name="rolling-back-from-the-preview"></a>Eseguire il rollback dall'anteprima 
Per eseguire il rollback dall'anteprima, eliminare tutti i nodi tranne uno. Non importa quali nodi si eliminano, ma assicurarsi di avere almeno un nodo nel gateway logico. È possibile eliminare un nodo disinstallando il gateway nel computer o usando il portale di Azure. Nella pagina **Data factory** del portale di Azure fare clic su Servizi collegati per avviare la pagina **Servizi collegati**. Selezionare il gateway per avviare la pagina **Gateway**. Nella pagina Gateway è possibile visualizzare i nodi associati al gateway. La pagina consente di eliminare un nodo dal gateway.
 
Dopo l'eliminazione fare clic su **Funzionalità in anteprima** nella stessa pagina del portale di Azure e disabilitare la funzionalità di anteprima. Il gateway è stato reimpostato come gateway a un nodo con disponibilità generale.


## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:
- [Gateway di gestione dati](data-factory-data-management-gateway.md): offre una panoramica dettagliata del gateway.
- [Spostare dati tra archivi dati locali e cloud](data-factory-move-data-between-onprem-and-cloud.md): contiene una procedura dettagliata con le istruzioni per usare un gateway a nodo singolo. 
