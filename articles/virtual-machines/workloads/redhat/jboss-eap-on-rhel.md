---
title: Avvio rapido - Distribuire JBoss Enterprise Application (EAP) su Red Hat Enterprise Linux (RHEL) in una VM e un set di scalabilità di macchine virtuali di Azure
description: Come distribuire applicazioni aziendali Java con Red Hat JBoss EAP in una VM RHEL e un set di scalabilità di macchine virtuali di Azure.
author: theresa-nguyen
ms.author: bicnguy
ms.topic: quickstart
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.assetid: 8a4df7bf-be49-4198-800e-db381cda98f5
ms.date: 10/30/2020
ms.openlocfilehash: d97148393d3158e38f9740d4a8f8e17dd04326d5
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93135118"
---
# <a name="deploy-enterprise-java-applications-to-azure-with-jboss-eap-on-red-hat-enterprise-linux"></a>Distribuire applicazioni aziendali Java in Azure con JBoss EAP su Red Hat Enterprise Linux

Questi modelli di avvio rapido illustrano come distribuire [JBoss EAP](https://www.redhat.com/en/technologies/jboss-middleware/application-platform) con [RHEL](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux) in macchine virtuali (VM) e set di scalabilità di macchine virtuali di Azure. Si userà un'app Java di esempio per convalidare la distribuzione. JBoss EAP è una piattaforma server di applicazioni open source. Offre sicurezza, scalabilità e prestazioni di livello aziendale per le applicazioni Java. RHEL è una piattaforma di sistema operativo open source. Supporta il ridimensionamento delle app esistenti e l'implementazione di tecnologie emergenti in tutti gli ambienti. JBoss EAP e RHEL offrono tutto ciò che serve per creare, eseguire, distribuire e gestire applicazioni Java aziendali in qualsiasi ambiente. Rappresentano un'ottima soluzione open source per ambienti locali, virtuali e cloud privati, pubblici o ibridi.

## <a name="prerequisite"></a>Prerequisito 

* Un account Azure con una sottoscrizione attiva. Per ottenere una sottoscrizione di Azure, attivare i [crediti Azure per sottoscrittori di Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) o [creare un account gratuito](https://azure.microsoft.com/pricing/free-trial).

* Installazione di JBoss EAP: è necessario un account Red Hat con entitlement RHSM (Red Hat Subscription Management) per JBoss EAP. Questo entitlement consentirà di scaricare la versione di JBoss EAP testata e certificata per Red Hat.  Se non si ha un entitlement EAP, ottenere una [sottoscrizione di valutazione di JBoss EAP](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) prima di iniziare. Per creare una nuova sottoscrizione di Red Hat, passare a [Red Hat Customer Portal](https://access.redhat.com/) e configurare un account.
F
* [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

* Opzioni RHEL: scegliere tra l'opzione con pagamento in base al consumo e BYOS (Bring-Your-Own-Subscription). Con BYOS è necessario attivare l'immagine Gold di [Red Hat Cloud Access](https://access.redhat.com/) per RHEL prima di distribuire il modello di avvio rapido.

## <a name="java-ee--jakarata-ee-application-migration"></a>Migrazione di applicazioni Java EE/Jakarata EE

### <a name="migrate-to-jboss-eap"></a>Eseguire la migrazione a JBoss EAP
JBoss EAP 7.2 e 7.3 sono implementazioni certificate delle specifiche Java Enterprise Edition (Java EE) 8 e Jakarta EE 8. JBoss EAP fornisce opzioni preconfigurate per funzionalità come il clustering a disponibilità elevata, la messaggistica e la cache distribuita. Fornisce inoltre agli utenti una varietà di API e servizi per scrivere, distribuire ed eseguire applicazioni.  Per altre informazioni su JBoss EAP, vedere [Introduction to JBoss EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html-single/introduction_to_jboss_eap/index) o [Introduction to JBoss EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/introduction_to_jboss_eap/index).

 #### <a name="applications-on-jboss-eap"></a>Applicazioni in JBoss EAP

* Applicazioni di servizi Web. I servizi Web offrono una modalità standard di interazione tra applicazioni software diverse. Ogni applicazione può essere eseguita su piattaforme e framework diversi. Questi servizi Web facilitano le comunicazioni tra sottosistemi interni ed eterogenei. Per altre informazioni, vedere [Developing Web Services Applications on EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_web_services_applications/index) o [Developing Web Services Applications on EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_web_services_applications/index).

* Applicazioni Enterprise Java Beans (EJB). EJB 3.2 è un'API per lo sviluppo di applicazioni Java EE e Jakarta EE distribuite, transazionali, sicure e portabili. EJB usa componenti lato server denominati Enterprise Beans per implementare la logica di business di un'applicazione in modo separato, incoraggiandone il riutilizzo. Per altre informazioni, vedere [Developing EJB Applications on EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_ejb_applications/index) o [Developing EJB Applications on EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_ejb_applications/index).

* Applicazioni Hibernate. Gli sviluppatori e gli amministratori possono sviluppare e distribuire applicazioni JPA (Java Persistence API) o Hibernate con JBoss EAP. Hibernate Core è un framework di mapping relazionale a oggetti per il linguaggio Java. Consente di eseguire il mapping di un modello di dominio orientato a oggetti a un database relazionale, permettendo così alle applicazioni di evitare l'interazione diretta con il database. Hibernate Entity Manager implementa le interfacce di programmazione e le regole del ciclo di vita conformemente alla [specifica JPA 2.1](https://www.jcp.org/en/jsr/overview). Insieme a Hibernate Annotations, questo wrapper implementa una soluzione JPA completa (e autonoma) basata su Hibernate Core. Per altre informazioni su Hibernate, vedere [JPA on EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/development_guide/java_persistence_api) o [Jakarta Persistence on EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/development_guide/java_persistence_api).

#### <a name="red-hat-migration-toolkit-for-applications-mta"></a>Red Hat Migration Toolkit for Applications (MTA)
[Red Hat Migration Toolkit for Applications (MTA)](https://developers.redhat.com/products/mta/overview) è uno strumento di migrazione per i server applicazioni Java che può essere usato per eseguire la migrazione da un altro server applicazioni a JBoss EAP. Funziona con i plug-in IDE per [Eclipse IDE](https://www.eclipse.org/ide/), [Red Hat CodeReady Workspaces](https://developers.redhat.com/products/codeready-workspaces/overview) e [Visual Studio Code (VS Code)](https://code.visualstudio.com/docs/languages/java) per Java.  MTA è uno strumento gratuito e open source che:
* Automatizza l'analisi delle applicazioni
* Supporta la stima dell'impegno
* Accelera la migrazione del codice
* Supporta la containerizzazione
* Si integra con Azure Workload Builder

### <a name="migrate-jboss-eap-from-on-premises-to-azure"></a>Eseguire la migrazione di JBoss EAP dall'ambiente locale ad Azure
Tramite l'offerta di Azure Marketplace è possibile installare ed effettuare il provisioning di JBoss EAP su RHEL in macchine virtuali di Azure in meno di 20 minuti. È possibile accedere a queste offerte da [Azure Marketplace](https://azuremarketplace.microsoft.com/).

Nel Marketplace vengono offerte diverse combinazioni di versioni EAP e RHEL per soddisfare le diverse esigenze. JBoss EAP è sempre offerto con il modello BYOS (Bring-Your-Own-Subscription), ma per il sistema operativo RHEL è possibile scegliere tra BYOS e il pagamento in base al consumo. L'offerta di Azure Marketplace include opzioni del piano di JBoss EAP su RHEL come VM autonome o in cluster:

* JBoss EAP 7.2 su VM RHEL 7.7 (con pagamento in base al consumo)
* JBoss EAP 7.2 su VM RHEL 8.0 (con pagamento in base al consumo)
* JBoss EAP 7.3 su VM RHEL 8.0 (con pagamento in base al consumo)
* JBoss EAP 7.2 su VM RHEL 7.7 (BYOS)
* JBoss EAP 7.2 su VM RHEL 8.0 (BYOS)
* JBoss EAP 7.3 su VM RHEL 8.0 (BYOS)

Oltre alle offerte di Azure Marketplace, sono disponibili modelli di avvio rapido utili per iniziare il percorso di migrazione ad Azure. Questi modelli di avvio rapido includono script e modelli di Azure Resource Manager predefiniti per la distribuzione di JBoss EAP su RHEL in varie configurazioni e combinazioni di versioni. Si otterrà:

* Bilanciamento del carico
* IP privato per il bilanciamento del carico e le VM
* Rete virtuale con una sola subnet
* Configurazione della VM (in cluster o autonoma)
* Applicazione Java di esempio

L'architettura della soluzione per questi modelli include:

* JBoss EAP su VM RHEL autonoma
* JBoss EAP in cluster su più VM RHEL
* JBoss EAP in cluster con set di scalabilità di macchine virtuali

#### <a name="linux-workload-migration-for-jboss-eap"></a>Migrazione di carichi di lavoro Linux per JBoss EAP
Azure Workload Builder semplifica il processo di modello di verifica, valutazione e migrazione di app Java locali ad Azure. Workload Builder si integra con lo strumento di individuazione Azure Migrate per identificare i server JBoss EAP. Genera quindi in modo dinamico un playbook Ansible per la distribuzione dei server JBoss EAP. Sfrutta lo strumento Red Hat MTA per eseguire la migrazione dei server da altri server applicazioni a JBoss EAP. Questi sono i passaggi per una migrazione semplificata:
* Valutazione: clustering di JBoss EAP tramite una VM o un set di scalabilità di macchine virtuali di Azure
* Analisi: analisi delle applicazioni e dell'infrastruttura
* Configurazione dell'infrastruttura: creazione di un profilo del carico di lavoro
* Distribuzione e test: distribuzione, migrazione e test del carico di lavoro
* Configurazione post-distribuzione: integrazione con dati, monitoraggio, sicurezza, backup e altro

## <a name="server-configuration-choice"></a>Scelta della configurazione dei server

Per la distribuzione di una VM RHEL, è possibile scegliere tra la configurazione con pagamento in base al consumo o BYOS. La configurazione predefinita delle immagini di [Azure Marketplace](https://azuremarketplace.microsoft.com) è quella con pagamento in base al consumo. Distribuire una VM RHEL di tipo BYOS se si ha un'immagine del proprio sistema operativo RHEL. Prima di distribuire le VM o il set di scalabilità di macchine virtuali, verificare che l'account RHSM sia idoneo per BYOS tramite F Cloud Access.

JBoss EAP offre potenti funzionalità di gestione, oltre a funzionalità e API per le sue applicazioni. Queste funzionalità di gestione variano a seconda della modalità operativa usata per avviare JBoss EAP. JBoss EAP è supportato su RHEL e Windows Server. Offre una modalità operativa server autonoma per la gestione di istanze discrete, oltre a una modalità operativa del dominio gestito per la gestione di gruppi di istanze da un singolo punto di controllo. Nota: i domini gestiti da JBoss EAP non sono supportati in Microsoft Azure, poiché la funzionalità di disponibilità elevata è gestita dai servizi di infrastruttura di Azure. La variabile di ambiente *EAP_HOME* viene usata per indicare il percorso dell'installazione di JBoss EAP.

**Avviare JBoss EAP come server autonomo** : il comando seguente consente di avviare il servizio EAP in modalità autonoma.

```
$EAP_HOME/bin/standalone.sh
```
    
Questo script di avvio usa il file EAP_HOME/bin/standalone.conf per impostare alcune preferenze predefinite, come le opzioni di JVM. Le impostazioni possono essere personalizzate in questo file. JBoss EAP usa il file di configurazione standalone.xml per eseguire l'avvio in modalità autonoma per impostazione predefinita, ma può essere avviato anche con un'altra modalità. Per informazioni dettagliate sui file di configurazione autonomi disponibili e su come usarli, vedere la sezione [Stand-alone Server Configuration Files for EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files) o [Stand-alone Server Configuration Files for EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files). Per avviare JBoss EAP con una configurazione diversa, usare l'argomento --server-config. Ad esempio:
    
 ```
 $EAP_HOME/bin/standalone.sh --server-config=standalone-full.xml
 ```
    
Per un elenco completo di tutti gli argomenti di script di avvio disponibili e sulla relativa funzione, usare l'argomento --help o vedere la sezione [Server Runtime Arguments on EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime) o [Server Runtime Arguments on EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime).
    
JBoss EAP supporta anche la modalità cluster. Per altre informazioni, vedere [Clusters Overview on EAP 7.2](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuring_messaging/clusters_overview) o [ Clusters Overview on EAP 7.3](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuring_messaging/clusters_overview). La messaggistica cluster di JBoss EAP consente di raggruppare i server di messaggistica di JBoss EAP per condividere il carico di elaborazione dei messaggi. Ogni nodo attivo nel cluster è un server di messaggistica di JBoss EAP attivo, che gestisce i propri messaggi e le proprie connessioni.

## <a name="support-and-subscription-notes"></a>Note sul supporto e le sottoscrizioni
Questi modelli di avvio rapido vengono offerti come segue: 

- Il sistema operativo RHEL viene offerto con il modello con pagamento in base al consumo o BYOS tramite l'immagine Gold di Red Hat
- JBoss EAP viene offerto solo con il modello BYOS

#### <a name="using-rhel-os-with-payg-model"></a>Uso del sistema operativo RHEL con il modello con pagamento in base al consumo

Per impostazione predefinita, questi modelli di avvio rapido usano l'immagine di RHEL 7.7 o 8.0 su richiesta con pagamento in base al consumo disponibile nella raccolta di Azure. Alle immagini con pagamento in base al consumo vengono applicate ulteriori tariffe orarie di sottoscrizione RHEL oltre ai normali costi di calcolo, rete e archiviazione. Allo stesso tempo, l'istanza viene registrata nella sottoscrizione di Red Hat. Questo significa che si userà uno degli entitlement di cui si dispone. Questa immagine con pagamento in base al consumo comporterà una "doppia fatturazione". È possibile evitare questo problema compilando un'immagine RHEL personalizzata. Per altre informazioni, leggere questo articolo della Knowledge Base di Red Hat su [come effettuare il provisioning di una VM RHEL per Microsoft Azure](https://access.redhat.com/articles/uploading-rhel-image-to-azure). Oppure attivare l'immagine Gold di [Red Hat Cloud Access](https://access.redhat.com/) su RHEL.

Per altre informazioni sui prezzi delle VM con pagamento in base al consumo, vedere [Prezzi di Macchine virtuali Red Hat](https://azure.microsoft.com/pricing/details/virtual-machines/red-hat/). Per usare RHEL nel modello con pagamento in base al consumo, è necessaria una sottoscrizione di Azure con il metodo di pagamento specificato per [RHEL 7.7 Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux77-ARM) o [RHEL 8.0 Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux80-ARM). Con queste offerte occorre specificare un metodo di pagamento nella sottoscrizione di Azure.

#### <a name="using-rhel-os-with-byos-model"></a>Uso del sistema operativo RHEL con il modello BYOS

Per usare BYOS per il sistema operativo RHEL, è necessaria una sottoscrizione di Red Hat valida con entitlement per l'utilizzo del sistema operativo RHEL in Azure. Prima di distribuire questo modello di avvio rapido, completare i prerequisiti seguenti:

1. Verificare che alla sottoscrizione di Red Hat siano associati gli entitlement del sistema operativo RHEL e di JBoss EAP.
2. Autorizzare l'ID della sottoscrizione di Azure all'uso delle immagini RHEL con modello BYOS. Seguire la [documentazione di Red Hat Subscription Management (RHSM)](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/con-enable-subs) per completare la procedura, costituita dai passaggi seguenti:

    2.1 Abilitare Microsoft Azure come provider nel dashboard di Red Hat Cloud Access.

    2.2 Aggiungere gli ID di sottoscrizione di Azure.

    2.3 Abilitare nuovi prodotti per Cloud Access in Microsoft Azure.
    
    2.4 Attivare le immagini Gold di Red Hat per la sottoscrizione di Azure. Per altre informazioni, leggere il capitolo su come [abilitare e gestire le sottoscrizioni per Cloud Access](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure).

    2.5 Attendere che le immagini Gold di Red Hat siano disponibili nella sottoscrizione di Azure. In genere occorrono circa 3 ore dall'invio.
    
3. Accettare le condizioni di Azure Marketplace per le immagini RHEL con modello BYOS. È possibile completare questa procedura eseguendo i comandi dell'interfaccia della riga di comando di Azure, come illustrato di seguito. Per altre informazioni, vedere [Immagini Gold di tipo BYOS (Bring-Your-Own-Subscription) di Red Hat Enterprise Linux in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/byos). È importante eseguire la versione più recente dell'interfaccia della riga di comando di Azure.

    3.1 Avviare una sessione dell'interfaccia della riga di comando di Azure ed eseguire l'autenticazione con l'account Azure. Per assistenza, vedere [Accedere tramite l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli). Prima di continuare, assicurarsi di eseguire la versione più recente dell'interfaccia della riga di comando di Azure.

    3.2 Verificare che le immagini RHEL con modello BYOS siano disponibili nella sottoscrizione eseguendo il comando dell'interfaccia della riga di comando seguente. Se non vengono restituiti risultati, fare riferimento al passaggio 2 e verificare che la sottoscrizione di Azure sia attivata per le immagini RHEL con modello BYOS.
   
   ```
   az vm image list --offer rhel-byos --all
   ```

    3.3 Eseguire il comando seguente per accettare le condizioni del Marketplace rispettivamente per RHEL 7.7 BYOS e RHEL 8.0 BYOS.
   ```
   az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm77
   ``` 

   ```
   az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm8
   ``` 
   
4. La sottoscrizione è ora pronta per distribuire RHEL 7.7 o 8.0 BYOS in macchine virtuali di Azure.

#### <a name="using-jboss-eap-with-byos-model"></a>Uso di JBoss EAP con modello BYOS

JBoss EAP è disponibile in Azure solo tramite il modello BYOS. Durante la distribuzione di questo modello, è necessario specificare le credenziali di RHSM insieme all'ID pool RHSM con entitlement EAP validi. Se non si ha un entitlement EAP, ottenere una [sottoscrizione di valutazione di JBoss EAP](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation) prima di iniziare.

## <a name="how-to-consume"></a>Modalità di utilizzo

È possibile distribuire il modello nei tre modi seguenti:

- Tramite PowerShell. Distribuire il modello eseguendo i comandi seguenti: Per informazioni sull'installazione e la configurazione di Azure PowerShell, vedere [Azure PowerShell](https://docs.microsoft.com/powershell/azure/).

  ```
  New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri <raw link to the template which can be obtained from github>
  ```
    
- Tramite l'interfaccia della riga di comando di Azure. Distribuire il modello eseguendo i comandi seguenti: (Per informazioni sull'installazione e la configurazione dell'interfaccia della riga di comando di Azure, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)).

  ```
  az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  az group deployment create --resource-group <my-resource-group> --template-uri <raw link to the template which can be obtained from github>
  ```

- Usare il portale di Azure: è possibile eseguire la distribuzione nel portale di Azure usando i *modelli di avvio rapido di Azure* come indicato nella sezione seguente. Nel modello di avvio rapido fare clic sul pulsante **Distribuisci in Azure** o **Esplora in GitHub**.

## <a name="azure-quickstart-templates"></a>Modelli di avvio rapido di Azure

È possibile iniziare con uno dei modelli di avvio rapido di JBoss EAP su RHEL nella combinazione che soddisfa i propri obiettivi di distribuzione. Di seguito è riportato l'elenco dei modelli di avvio rapido disponibili.

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-standalone-rhel/"> JBoss EAP su RHEL (VM autonoma)</a>: questo modello distribuisce un'applicazione Web denominata JBoss-EAP in Azure su JBoss EAP 7.2 o 7.3 in esecuzione su una VM RHEL 7.7 o 8.0.

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-clustered-multivm-rhel/"> JBoss EAP su RHEL (più VM in cluster)</a>: questo modello distribuisce un'applicazione Web denominata eap-session-replication in un cluster JBoss EAP 7.2 o 7.3 in esecuzione su "N" VM RHEL 7.7 o 8.0. Il valore di "N" viene deciso dall'utente. Tutte le VM vengono aggiunte al pool back-end di un'istanza di Load Balancer.

* <a href="https://azure.microsoft.com/en-us/resources/templates/jboss-eap-clustered-vmss-rhel/"> JBoss EAP su RHEL (set di scalabilità di macchine virtuali in cluster)</a>: questo modello distribuisce un'applicazione Web denominata eap-session-replication in un cluster JBoss EAP 7.2 o 7.3 in esecuzione su istanze di set di scalabilità di macchine virtuali RHEL 7.7 o 8.0.

## <a name="resource-links"></a>Collegamenti alle risorse:

* [Vantaggi Azure Hybrid](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing)
* [Configurare un'app Java per il servizio app di Azure](https://docs.microsoft.com/azure/app-service/configure-language-java)
* [JBoss EAP su Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [JBoss EAP nel servizio app di Azure per Linux](https://docs.microsoft.com/azure/app-service/quickstart-java) - Avvio rapido
* [Come distribuire JBoss EAP nel servizio app di Azure](https://github.com/JasonFreeberg/jboss-on-app-service) - Esercitazione

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su [JBoss EAP 7.2](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.2/)
* Altre informazioni su [JBoss EAP 7.3](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.3/)
* Altre informazioni su [Red Hat Subscription Management](https://access.redhat.com/products/red-hat-subscription-management)
* Documentazione Microsoft per [Red Hat su Azure](https://aka.ms/rhel-docs)
* Distribuire [JBoss EAP su una VM RHEL o un set di scalabilità di macchine virtuali da Azure Marketplace](https://aka.ms/AMP-JBoss-EAP)
* Distribuire [JBoss EAP su una VM RHEL o un set di scalabilità di macchine virtuali da un modello di avvio rapido di Azure](https://aka.ms/Quickstart-JBoss-EAP)
