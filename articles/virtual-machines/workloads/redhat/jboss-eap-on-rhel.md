---
title: 'Avvio rapido: Distribuire JBoss Enterprise Application Platform (EAP) con Red Hat Enterprise Linux (RHEL) in VM e set di scalabilità di macchine virtuali di Azure'
description: Come distribuire applicazioni aziendali Java con Red Hat JBoss EAP in VM e set di scalabilità di macchine virtuali RHEL di Azure.
author: theresa-nguyen
ms.author: bicnguy
ms.topic: quickstart
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.assetid: 8a4df7bf-be49-4198-800e-db381cda98f5
ms.date: 10/30/2020
ms.openlocfilehash: bab84b12c871c621b5a317ba8b47f9b18c91bff3
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500190"
---
# <a name="deploy-enterprise-java-applications-to-azure-with-jboss-eap-on-red-hat-enterprise-linux"></a>Distribuire applicazioni aziendali Java in Azure con JBoss EAP su Red Hat Enterprise Linux

I modelli di avvio rapido di Azure descritti in questo articolo illustrano come distribuire [JBoss Enterprise Application Platform (EAP)](https://www.redhat.com/en/technologies/jboss-middleware/application-platform) con [Red Hat Enterprise Linux (RHEL)](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux) in macchine virtuali e set di scalabilità di macchine virtuali di Azure. Si userà un'app Java di esempio per convalidare la distribuzione. 

JBoss EAP è una piattaforma server di applicazioni open source. Offre sicurezza, scalabilità e prestazioni di livello aziendale per le applicazioni Java. RHEL è una piattaforma di sistema operativo open source. Supporta il ridimensionamento delle app esistenti e l'implementazione di tecnologie emergenti in tutti gli ambienti. 

JBoss EAP e RHEL includono tutto il necessario per compilare, eseguire, distribuire e gestire applicazioni aziendali Java in qualsiasi ambiente. La combinazione rappresenta una soluzione open source per ambienti locali e virtuali, oltre che per cloud privati, pubblici o ibridi.

## <a name="prerequisites"></a>Prerequisiti 

* Un account Azure con una sottoscrizione attiva. Per ottenere una sottoscrizione di Azure, attivare i [crediti Azure per sottoscrittori di Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) o [creare un account gratuito](https://azure.microsoft.com/pricing/free-trial).

* Installazione di JBoss EAP. È necessario un account Red Hat con entitlement di RHSM (Red Hat Subscription Management) per JBoss EAP. Questo entitlement consentirà di scaricare la versione di JBoss EAP testata e certificata per Red Hat.  

  Se non si ha un entitlement EAP, ottenere una [sottoscrizione di valutazione di JBoss EAP](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) prima di iniziare. Per creare una nuova sottoscrizione di Red Hat, passare a [Red Hat Customer Portal](https://access.redhat.com/) e configurare un account.

* [Interfaccia della riga di comando di Azure](/cli/azure/overview).

* Opzioni di RHEL. Scegliere tra l'opzione con pagamento in base al consumo e BYOS (Bring Your Own Subscription). Con l'opzione BYOS, è necessario attivare l'immagine Gold di [Red Hat Cloud Access](https://access.redhat.com/) per RHEL prima di distribuire il modello di avvio rapido.

## <a name="java-ee-and-jakarta-ee-application-migration"></a>Migrazione di applicazioni Java EE e Jakarta EE

### <a name="migrate-to-jboss-eap"></a>Eseguire la migrazione a JBoss EAP
JBoss EAP 7.2 e 7.3 sono implementazioni certificate delle specifiche Java Enterprise Edition (Java EE) 8 e Jakarta EE 8. JBoss EAP fornisce opzioni preconfigurate per funzionalità come il clustering a disponibilità elevata, la messaggistica e la cache distribuita. Consente inoltre agli utenti di scrivere, distribuire ed eseguire applicazioni usando l'ampia varietà di API e servizi forniti da JBoss EAP.  

Per altre informazioni su JBoss EAP, vedere [Introduction to JBoss EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html-single/introduction_to_jboss_eap/index) o [Introduction to JBoss EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/introduction_to_jboss_eap/index).

 #### <a name="applications-on-jboss-eap"></a>Applicazioni in JBoss EAP

* **Applicazioni di servizi Web**. I servizi Web offrono un modo standard per implementare l'interoperabilità tra applicazioni software. Ogni applicazione può essere eseguita su piattaforme e framework diversi. Questi servizi Web facilitano le comunicazioni tra sottosistemi interni ed eterogenei. 

  Per altre informazioni, vedere [Developing Web Services Applications on EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_web_services_applications/index) o [Developing Web Services Applications on EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_web_services_applications/index).

* **Applicazioni Enterprise Java Beans (EJB)** . EJB 3.2 è un'API per lo sviluppo di applicazioni Java EE e Jakarta EE distribuite, transazionali, sicure e portabili. EJB usa i componenti lato server Enterprise Beans per implementare la logica di business di un'applicazione in un modo disaccoppiato che ne incoraggia il riutilizzo. 

  Per altre informazioni, vedere [Developing EJB Applications on EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_ejb_applications/index) o [Developing EJB Applications on EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_ejb_applications/index).

* **Applicazioni Hibernate**. Sviluppatori e amministratori possono sviluppare e distribuire applicazioni JPA (Java Persistence API) o Hibernate con JBoss EAP. Hibernate Core è un framework di mapping relazionale a oggetti per il linguaggio Java. Fornisce un framework per eseguire il mapping di un modello di dominio orientato a oggetti a un database relazionale, in modo da evitare l'interazione diretta tra le applicazioni e il database. 

  Hibernate Entity Manager implementa le interfacce di programmazione e le regole del ciclo di vita conformemente alla [specifica JPA 2.1](https://www.jcp.org/en/jsr/overview). Insieme a Hibernate Annotations, questo wrapper implementa una soluzione JPA completa (e autonoma) basata su Hibernate Core. 
  
  Per altre informazioni su Hibernate, vedere [JPA on EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/development_guide/java_persistence_api) o [Jakarta Persistence on EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/development_guide/java_persistence_api).

#### <a name="red-hat-migration-toolkit-for-applications"></a>Red Hat Migration Toolkit for Applications
[Red Hat Migration Toolkit for Applications (MTA)](https://developers.redhat.com/products/mta/overview) è uno strumento di migrazione per i server applicazioni Java che può essere usato per eseguire la migrazione da un altro server applicazioni a JBoss EAP. Funziona con i plug-in IDE per l'[IDE Eclipse](https://www.eclipse.org/ide/), [Red Hat CodeReady Workspaces](https://developers.redhat.com/products/codeready-workspaces/overview) e [Visual Studio Code (VS Code)](https://code.visualstudio.com/docs/languages/java) per Java. 

MTA è uno strumento gratuito e open source che:
* Automatizza l'analisi delle applicazioni.
* Supporta la stima del lavoro richiesto.
* Accelera la migrazione del codice.
* Supporta la containerizzazione.
* Si integra con Azure Workload Builder.

### <a name="migrate-jboss-eap-from-on-premises-to-azure"></a>Eseguire la migrazione di JBoss EAP dall'ambiente locale ad Azure
Tramite l'offerta di Azure Marketplace è possibile installare ed effettuare il provisioning di JBoss EAP su RHEL in macchine virtuali di Azure in meno di 20 minuti. È possibile accedere a queste offerte da [Azure Marketplace](https://azuremarketplace.microsoft.com/).

Nel Marketplace vengono offerte varie combinazioni di versioni EAP e RHEL per soddisfare specifici requisiti. JBoss EAP è sempre offerto con il modello BYOS, ma per il sistema operativo RHEL è possibile scegliere tra BYOS e il pagamento in base al consumo. L'offerta di Azure Marketplace include opzioni del piano di JBoss EAP su RHEL come VM autonome o in cluster:

* JBoss EAP 7.2 su VM RHEL 7.7 (con pagamento in base al consumo)
* JBoss EAP 7.2 su VM RHEL 8.0 (con pagamento in base al consumo)
* JBoss EAP 7.3 su VM RHEL 8.0 (con pagamento in base al consumo)
* JBoss EAP 7.2 su VM RHEL 7.7 (BYOS)
* JBoss EAP 7.2 su VM RHEL 8.0 (BYOS)
* JBoss EAP 7.3 su VM RHEL 8.0 (BYOS)

Oltre alle offerte di Azure Marketplace, è possibile usare modelli di avvio rapido per iniziare il percorso di migrazione ad Azure. Questi modelli di avvio rapido includono script e modelli di Azure Resource Manager predefiniti per la distribuzione di JBoss EAP su RHEL in varie configurazioni e combinazioni di versioni. Si otterrà:

* Un sistema di bilanciamento del carico.
* Un indirizzo IP privato per il bilanciamento del carico e le VM.
* Una rete virtuale con una singola subnet.
* La configurazione della VM (in cluster o autonoma).
* Un'applicazione Java di esempio.

L'architettura della soluzione per questi modelli include:

* JBoss EAP su VM RHEL autonoma.
* JBoss EAP in cluster su più VM RHEL.
* JBoss EAP in cluster tramite set di scalabilità di macchine virtuali di Azure.

#### <a name="linux-workload-migration-for-jboss-eap"></a>Migrazione di carichi di lavoro Linux per JBoss EAP
Azure Workload Builder semplifica il modello di verifica, la valutazione e il processo di migrazione ad Azure delle app Java locali. Workload Builder si integra con lo strumento di individuazione Azure Migrate per identificare i server JBoss EAP. Genera quindi dinamicamente un playbook Ansible per la distribuzione dei server JBoss EAP. Usa lo strumento Red Hat MTA per eseguire la migrazione dei server da altri server applicazioni a JBoss EAP. 

Questi sono i passaggi per una migrazione semplificata:
1. **Valutazione**. Valutare i cluster di JBoss EAP tramite una VM o un set di scalabilità di macchine virtuali di Azure.
1. **Analisi**. Analizzare le applicazioni e l'infrastruttura.
1. **Configurazione dell'infrastruttura**. Creare un profilo del carico di lavoro.
1. **Distribuzione e test**. Distribuire, eseguire la migrazione e testare il carico di lavoro.
1. **Configurazione post-distribuzione**. Integrare dati, monitoraggio, sicurezza, backup e altro ancora.

## <a name="server-configuration-choice"></a>Scelta della configurazione dei server

Per la distribuzione della VM RHEL, è possibile scegliere tra la configurazione con pagamento in base al consumo o BYOS. La configurazione predefinita delle immagini di [Azure Marketplace](https://azuremarketplace.microsoft.com) è quella con pagamento in base al consumo. Distribuire una VM RHEL di tipo BYOS se si ha un'immagine del proprio sistema operativo RHEL. Prima di distribuire le VM o il set di scalabilità di macchine virtuali, verificare che l'account RHSM abbia l'entitlement per BYOS tramite F Cloud Access.

JBoss EAP offre potenti funzionalità di gestione, oltre a funzionalità e API per le sue applicazioni. Queste funzionalità di gestione variano a seconda della modalità operativa usata per avviare JBoss EAP. JBoss EAP è supportato su RHEL e Windows Server. Offre una modalità operativa server autonoma per la gestione di istanze discrete, oltre a una modalità operativa del dominio gestito per la gestione di gruppi di istanze da un singolo punto di controllo. 

> [!NOTE]
> I domini gestiti da JBoss EAP non sono supportati in Microsoft Azure, perché i servizi dell'infrastruttura di Azure gestiscono la funzionalità di disponibilità elevata. 

La variabile di ambiente `EAP_HOME` indica il percorso dell'installazione di JBoss EAP. Usare il comando seguente per avviare il servizio JBoss EAP in modalità autonoma:

```
$EAP_HOME/bin/standalone.sh
```
    
Questo script di avvio usa il file EAP_HOME/bin/standalone.conf per impostare alcune preferenze predefinite, come le opzioni di JVM. È possibile personalizzare le impostazioni in questo file. JBoss EAP usa il file di configurazione standalone.xml per eseguire l'avvio in modalità autonoma per impostazione predefinita, ma può essere avviato anche con un'altra modalità. 

Per informazioni dettagliate sui file di configurazione autonomi disponibili e su come usarli, vedere [Standalone Server Configuration Files for EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files) o [Standalone Server Configuration Files for EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files). 

Per avviare JBoss EAP con una configurazione diversa, usare l'argomento `--server-config`. Ad esempio:
    
 ```
 $EAP_HOME/bin/standalone.sh --server-config=standalone-full.xml
 ```
    
Per un elenco completo di tutti gli argomenti dello script di avvio disponibili e dei rispettivi scopi, usare l'argomento `--help`. Per altre informazioni, vedere [Server Runtime Arguments on EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime) o [Server Runtime Arguments on EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime).
    
JBoss EAP supporta anche la modalità cluster. La messaggistica cluster di JBoss EAP consente di raggruppare i server di messaggistica di JBoss EAP per condividere il carico di elaborazione dei messaggi. Ogni nodo attivo nel cluster è un server di messaggistica di JBoss EAP attivo, che gestisce i propri messaggi e le proprie connessioni. Per altre informazioni, vedere [Clusters Overview on EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuring_messaging/clusters_overview) o [ Clusters Overview on EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuring_messaging/clusters_overview). 

## <a name="support-and-subscription-notes"></a>Note sul supporto e le sottoscrizioni
Questi modelli di avvio rapido vengono offerti come segue: 

- Il sistema operativo RHEL viene offerto con il modello con pagamento in base al consumo o BYOS tramite l'immagine Gold di Red Hat.
- JBoss EAP viene offerto solo con il modello BYOS.

#### <a name="using-rhel-os-with-the-payg-model"></a>Uso del sistema operativo RHEL con il modello con pagamento in base al consumo

Per impostazione predefinita, questi modelli di avvio rapido usano l'immagine di RHEL 7.7 o 8.0 su richiesta con pagamento in base al consumo disponibile in Azure Marketplace. Alle immagini con pagamento in base al consumo vengono applicate ulteriori tariffe orarie di sottoscrizione RHEL oltre ai normali costi di calcolo, rete e archiviazione. Allo stesso tempo, l'istanza viene registrata nella sottoscrizione di Red Hat. Questo significa che si userà uno degli entitlement di cui si dispone. 

Questa immagine con pagamento in base al consumo comporterà una "doppia fatturazione". È possibile evitare questo problema compilando un'immagine RHEL personalizzata. Per altre informazioni, leggere questo articolo della knowledge base di Red Hat su [come effettuare il provisioning di una VM RHEL per Microsoft Azure](https://access.redhat.com/articles/uploading-rhel-image-to-azure). Oppure attivare l'immagine Gold di [Red Hat Cloud Access](https://access.redhat.com/) su RHEL.

Per altre informazioni sui prezzi delle VM con pagamento in base al consumo, vedere [Prezzi di Red Hat Enterprise Linux](https://azure.microsoft.com/pricing/details/virtual-machines/red-hat/). Per usare RHEL nel modello con pagamento in base al consumo, è necessaria una sottoscrizione di Azure con il metodo di pagamento specificato per [RHEL 7.7 in Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux77-ARM) o [RHEL 8.0 in Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux80-ARM). Con queste offerte occorre specificare un metodo di pagamento nella sottoscrizione di Azure.

#### <a name="using-rhel-os-with-the-byos-model"></a>Uso del sistema operativo RHEL con il modello BYOS

Per usare BYOS per il sistema operativo RHEL, è necessaria una sottoscrizione di Red Hat valida con entitlement per l'utilizzo del sistema operativo RHEL in Azure. Prima di distribuire il sistema operativo RHEL con il modello BYOS, completare i prerequisiti seguenti:

1. Verificare che alla sottoscrizione di Red Hat siano associati gli entitlement del sistema operativo RHEL e di JBoss EAP.
2. Autorizzare l'ID della sottoscrizione di Azure all'uso delle immagini RHEL con modello BYOS. Seguire la [documentazione di Red Hat Subscription Management](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/con-enable-subs) per completare la procedura, costituita dai passaggi seguenti:

   1. Abilitare Microsoft Azure come provider nel dashboard di Red Hat Cloud Access.

   1. Aggiungere gli ID di sottoscrizione di Azure.

   1. Abilitare nuovi prodotti per Cloud Access in Microsoft Azure.
    
   1. Attivare le immagini Gold di Red Hat per la sottoscrizione di Azure. Per altre informazioni, vedere [Immagini Gold di Red Hat in Microsoft Azure](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure).

   1. Attendere che le immagini Gold di Red Hat siano disponibili nella sottoscrizione di Azure. In genere occorrono circa tre ore dall'invio.
    
3. Accettare le condizioni per l'utilizzo di Azure Marketplace per le immagini RHEL con modello BYOS. È possibile completare questo processo eseguendo i comandi seguenti dell'interfaccia della riga di comando di Azure. Per altre informazioni, vedere la documentazione sulle [immagini Gold di tipo BYOS di RHEL in Azure](./byos.md). È importante eseguire la versione più recente dell'interfaccia della riga di comando di Azure.

   1. Aprire una sessione dell'interfaccia della riga di comando di Azure ed eseguire l'autenticazione con l'account Azure. Per assistenza, vedere [Accedere con l'interfaccia della riga di comando di Azure](/cli/azure/authenticate-azure-cli).

   1. Verificare che le immagini RHEL con modello BYOS siano disponibili nella sottoscrizione eseguendo il comando seguente dell'interfaccia della riga di comando. Se non vengono restituiti risultati, verificare che la sottoscrizione di Azure sia attivata per le immagini RHEL con modello BYOS.
   
      ```
      az vm image list --offer rhel-byos --all
      ```

   1. Eseguire il comando seguente per accettare le condizioni del Marketplace rispettivamente per RHEL 7.7 BYOS e RHEL 8.0 BYOS:
      ```
      az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm77
      ``` 

      ```
      az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm8
      ``` 
   
La sottoscrizione è ora pronta per distribuire RHEL 7.7 o 8.0 BYOS in macchine virtuali di Azure.

#### <a name="using-jboss-eap-with-the-byos-model"></a>Uso di JBoss EAP con il modello BYOS

JBoss EAP è disponibile in Azure solo tramite il modello BYOS. Durante la distribuzione di questo modello, è necessario specificare le credenziali di RHSM insieme all'ID pool RHSM con entitlement di EAP validi. Se non si hanno entitlement di EAP, ottenere una [sottoscrizione di valutazione di JBoss EAP](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) prima di iniziare.

## <a name="deployment-options"></a>Opzioni di distribuzione

È possibile distribuire il modello nei modi seguenti:

- **PowerShell**. Distribuire il modello eseguendo i comandi seguenti: 

  ```
  New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri <raw link to the template which can be obtained from github>
  ```
 
  Per informazioni sull'installazione e la configurazione di Azure PowerShell, vedere la [documentazione di PowerShell](/powershell/azure/).  

- **Interfaccia della riga di comando di Azure**. Distribuire il modello eseguendo i comandi seguenti:

  ```
  az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  az group deployment create --resource-group <my-resource-group> --template-uri <raw link to the template which can be obtained from github>
  ```

  Per informazioni sull'installazione e la configurazione dell'interfaccia della riga di comando di Azure, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

- **Portale di Azure**. È possibile eseguire la distribuzione nel portale di Azure usando i modelli di avvio rapido di Azure come indicato nella sezione seguente. Nel modello di avvio rapido selezionare il pulsante **Distribuisci in Azure** o **Esplora in GitHub**.

## <a name="azure-quickstart-templates"></a>Modelli di avvio rapido di Azure

È possibile iniziare con uno dei modelli di avvio rapido seguenti per JBoss EAP su RHEL che soddisfi i propri obiettivi di distribuzione:

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-standalone-rhel/"> JBoss EAP su RHEL (VM autonoma)</a>. Questo modello distribuisce un'applicazione Web denominata JBoss-EAP in Azure su JBoss EAP 7.2 o 7.3 in esecuzione su una VM RHEL 7.7 o 8.0.

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-clustered-multivm-rhel/"> JBoss EAP su RHEL (più VM in cluster)</a>. Questo modello distribuisce un'applicazione Web denominata eap-session-replication in un cluster di JBoss EAP 7.2 o 7.3 in esecuzione su *n* VM RHEL 7.7 o 8.0. L'utente decide il valore di *n*. Tutte le VM vengono aggiunte al pool back-end di un sistema di bilanciamento del carico.

* <a href="https://azure.microsoft.com/en-us/resources/templates/jboss-eap-clustered-vmss-rhel/"> JBoss EAP su RHEL (set di scalabilità di macchine virtuali in cluster)</a>. Questo modello distribuisce un'applicazione Web denominata eap-session-replication in un cluster di JBoss EAP 7.2 o 7.3 in esecuzione su set di scalabilità di macchine virtuali RHEL 7.7 o 8.0.

## <a name="resource-links"></a>Collegamenti alle risorse

* [Vantaggio Azure Hybrid](../../windows/hybrid-use-benefit-licensing.md)
* [Configurare un'app Java per il servizio app di Azure](../../../app-service/configure-language-java.md)
* [JBoss EAP su Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [JBoss EAP nel servizio app di Azure per Linux](../../../app-service/quickstart-java.md)
* [Distribuire JBoss EAP nel servizio app di Azure](https://github.com/JasonFreeberg/jboss-on-app-service)

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su [JBoss EAP 7.2](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.2/).
* Altre informazioni su [JBoss EAP 7.3](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.3/).
* Altre informazioni su [Red Hat Subscription Management](https://access.redhat.com/products/red-hat-subscription-management).
* Informazioni sui [carichi di lavoro Red Hat in Azure](./overview.md).
* Distribuire [JBoss EAP su una VM o un set di scalabilità di macchine virtuali RHEL da Azure Marketplace](https://aka.ms/AMP-JBoss-EAP).
* Distribuire [JBoss EAP su una VM o un set di scalabilità di macchine virtuali RHEL dai modelli di avvio rapido di Azure](https://aka.ms/Quickstart-JBoss-EAP).