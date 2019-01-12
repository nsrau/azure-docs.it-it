---
title: Creare una soluzione di app con distribuzione geografica con Azure e Azure Stack | Microsoft Docs
description: Informazioni su come creare una soluzione di app con distribuzione geografica con Azure e Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: mabrigg
ms.reviewer: anajod
ms.openlocfilehash: 85400269be13295161aaff5936a6ae8c7d503b34
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2019
ms.locfileid: "54245872"
---
# <a name="tutorial-create-a-geo-distributed-app-solution-with-azure-and-azure-stack"></a>Esercitazione: Creare una soluzione di app con distribuzione geografica con Azure e Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Informazioni su come indirizzare il traffico a endpoint specifici in base a varie metriche usando il modello di App distribuite a livello geografico. Creazione di una gestione traffico con la configurazione di endpoint e il routing geografico in base al profilo assicura informazioni venga indirizzate per gli endpoint basati sui requisiti a livello di area, regolamentazione internazionale e aziendali e le esigenze dei dati.

In questa esercitazione si creerà un ambiente di esempio:

> [!div class="checklist"]
> - Creare un'App con distribuzione geografica.
> - Usare Gestione traffico come destinazione l'app.

## <a name="use-the-geo-distributed-apps-pattern"></a>Usare il modello di App con distribuzione geografica

Con il modello di distribuzione geografica, l'app può si estende su aree. Predefiniti per il cloud pubblico, ma alcuni utenti può richiedere che i dati rimangono nella relativa area geografica. È possibile indirizzare gli utenti nel cloud più adatto in base alle esigenze.

### <a name="issues-and-considerations"></a>Considerazioni e problemi

#### <a name="scalability-considerations"></a>Considerazioni sulla scalabilità

La soluzione che si creerà in questa esercitazione è di non supportare la scalabilità. Tuttavia, se usato in combinazione con altre tecnologie di Azure e in locale e soluzioni è possibile soddisfare i requisiti di scalabilità. Per informazioni sulla creazione di una soluzione hyrbid con la scalabilità automatica tramite il traffico di gestione, vedere [creare soluzioni di scalabilità tra cloud con Azure](azure-stack-solution-cloud-burst.md).

#### <a name="availability-considerations"></a>Considerazioni sulla disponibilità

Come nel caso di considerazioni sulla scalabilità, questa soluzione non direttamente in grado di soddisfare la disponibilità. Tuttavia, anche simile al nostro considerazioni sulla scalabilità, soluzioni e tecnologie locali e Azure, possono essere implementate all'interno di questa soluzione per garantire la disponibilità elevata per tutti i componenti coinvolti.

### <a name="when-to-use-this-pattern"></a>Quando usare questo modello

- L'organizzazione dispone di rami internazionali che richiedono la sicurezza a livello di area personalizzata e criteri di distribuzione.

- Le organizzazioni uffici del cliente effettua il pull di dipendenti, business e dati di struttura, che richiedono riportano attività per ogni normative locali e il fuso orario.

- I requisiti di scalabilità elevata possono essere soddisfatti con la scalabilità orizzontale delle applicazioni, con più distribuzioni di app eseguite in una singola area geografica, oltre che in diverse aree, per gestire i requisiti di carico particolarmente intenso.

### <a name="planning-the-topology"></a>Pianificazione della topologia

Prima di compilare un footprint dell'app distribuita, è utile disporre delle necessarie conoscenze seguenti:

-   **Dominio personalizzato per l'app:** definire il nome del dominio personalizzato che i clienti useranno per accedere all'app. Per l'app di esempio, il nome di dominio personalizzato è *www.scalableasedemo.com.*

-   **Dominio di Gestione traffico:** Un nome di dominio deve essere scelto durante la creazione di un' [profilo di gestione traffico di Azure](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-manage-profiles). Questo nome sarà combinato con il suffisso *trafficmanager.net* per registrare una voce di dominio gestita da Gestione traffico. Per l'app di esempio il nome scelto è *scalable-ase-demo*. Di conseguenza, è il nome di dominio completo gestito da Gestione traffico *scalable-ase-demo.trafficmanager.net*.

-   **Strategia per la scalabilità del footprint dell'app:** stabilire se il footprint dell'applicazione sarà distribuito tra più ambienti del servizio app in una singola area geografica, in più aree geografiche Una combinazione di entrambi gli approcci? La decisione dovrà essere basata sulla previsione dell'origine del traffico dei clienti, oltre che sull'efficacia della scalabilità del resto dell'infrastruttura di back-end di supporto di un'app. Ad esempio, con un'applicazione senza stato 100%, un'app può essere considerevolmente ridimensionata usando una combinazione di più ambienti del servizio App per ogni area di Azure, moltiplicato per gli ambienti del servizio App distribuiti tra più aree di Azure. Con oltre 15 aree globali di Azure disponibili per la selezione, i clienti possono veramente creare un footprint dell'applicazione iperscalabile in tutto il mondo. Per l'app di esempio usata per questo articolo sono stati creati tre ambienti del servizio app in una singola area di Azure (Stati Uniti centro-meridionali).

-   **Convenzione di denominazione per gli ambienti del servizio app:** ogni ambiente del servizio app richiede un nome univoco. Oltre a uno o due ambienti del servizio App, è utile avere una convenzione di denominazione per facilitare l'identificazione di ogni ambiente del servizio App. Per l'app di esempio è stata usata una convenzione di denominazione semplice. I nomi dei tre ambienti del servizio app sono *fe1ase*, *fe2ase* e *fe3ase*.

-   **Convenzione di denominazione per le app:** poiché verranno distribuite più istanze dell'app, è necessario definire un nome per ogni istanza dell'app distribuita. Gli ambienti del servizio App il nome dell'app stessa è utilizzabile tra più ambienti del servizio App. Poiché ogni ambiente del servizio App ha un suffisso univoco, gli sviluppatori possono scegliere di riutilizzare lo stesso nome di app in ogni ambiente. Ad esempio, uno sviluppatore può avere App denominate come segue: *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net*e così via. Per l'app in questo scenario, ogni istanza dell'app ha un nome univoco. I nomi delle istanze dell'app usati sono *webfrontend1*, *webfrontend2* e *webfrontend3*.

> [!Tip]  
> ![ibrido-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack è un'estensione di Azure. Azure Stack offre l'agilità e innovazione del cloud computing al tuo ambiente locale e abilitare l'unico cloud ibrido che consente di compilare e distribuire le app ibride ovunque.  
> 
> Il white paper [considerazioni sulla progettazione per applicazioni ibride](https://aka.ms/hybrid-cloud-applications-pillars) esamina i concetti fondamentali della qualità del software (selezione host, scalabilità, disponibilità, resilienza, gestibilità e sicurezza) per la progettazione, distribuzione e gestione applicazioni ibride. Le considerazioni di progettazione assistere nell'ottimizzazione della progettazione di applicazioni ibride, riducendo al minimo le sfide negli ambienti di produzione.

## <a name="part-1-create-a-geo-distributed-app"></a>Parte 1: Creare un'app con distribuzione geografica

In questa parte, si creerà un'app web.

> [!div class="checklist"]
> - Creare App web e pubblicare
> - Aggiungere codice al repository di Azure
> - Scegliere la compilazione di app a più destinazioni di cloud.
> - Gestire e configurare il processo di recapito Continuo

### <a name="prerequisites"></a>Prerequisiti

Sono necessari una sottoscrizione di Azure e l'installazione di Azure Stack.

### <a name="geo-distributed-app-steps"></a>Passaggi di app con distribuzione geografica

### <a name="obtain-a-custom-domain-and-configure-dns"></a>Ottenere un dominio personalizzato e configurare il server DNS

Aggiornare il DNS zone file foCreate web App e publishr del dominio. Azure AD possa verificare quindi la proprietà del nome di dominio personalizzato. Uso [DNS di Azure](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) per i record DNS di Azure/Office 365/esterni in Azure, oppure aggiungere la voce DNS in [un registrar DNS diverso](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

1. Registrare un dominio personalizzato con un Registrar di pubblico.

2. Accedere al registrar per il dominio. Un amministratore approvato potrebbe essere necessario per rendere gli aggiornamenti DNS.

3. Aggiornare il file di zona DNS per il dominio aggiungendo la voce DNS fornita da Azure AD. La voce DNS non modifica i comportamenti, ad esempio routing della posta elettronica o hosting web.

### <a name="create-web-apps-and-publish"></a>Creare App web e pubblicare

Configurare integrazione continua/recapito Continuo per distribuire App Web di Azure e Azure Stack e auto push delle modifiche per entrambi i cloud ibridi.

> [!Note]  
> Azure Stack con immagini appropriate sfruttati in esecuzione (Windows Server e SQL) e distribuzione del servizio App sono necessarie. Esaminare la documentazione del servizio App [prima di iniziare con il servizio App in Azure Stack](../azure-stack-app-service-before-you-get-started.md) sezione per l'operatore di Azure Stack.

#### <a name="add-code-to-azure-repos"></a>Aggiungere codice al repository di Azure

1. Accedi a Visual Studio con un **account che dispone dei diritti di creazione progetto** nei repository relativi a Azure.

    Ibrida Continuous Integration/recapito continuo (CI/CD) è possibile applicare al codice dell'applicazione e al codice dell'infrastruttura. Uso [modelli di Azure Resource Manager](https://azure.microsoft.com/resources/templates/) per lo sviluppo di entrambi i cloud privati e ospitati.

    ![Alt text](media/azure-stack-solution-geo-distributed/image1.JPG)

2. **Clonare il repository** , creare e aprire l'app web predefinita.

    ![Alt text](media/azure-stack-solution-geo-distributed/image2.png)

### <a name="create-web-app-deployment-in-both-clouds"></a>Creare la distribuzione di app web in entrambi i cloud

1.  Modificare il **WebApplication.csproj** file: Selezionare **Runtimeidentifier** e aggiungere **win10-x64**. (Vedere [Contained Deployment](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) documentazione.)

    ![Alt text](media/azure-stack-solution-geo-distributed/image3.png)

1.  **Archivia il codice nel repository Azure** tramite Team Explorer.

2.  Verificare che il **codice dell'applicazione** è stato archiviato in repository di Azure.

### <a name="create-the-build-definition"></a>Creare la definizione di compilazione

1. **Log in pipeline di Azure in** confermare possibilità di creare definizioni di compilazione.

2. Aggiungere **- r win10-x64** codice. Questa operazione è necessaria attivare una distribuzione autonoma con.NET Core.

    ![Alt text](media/azure-stack-solution-geo-distributed/image4.png)

3. **Eseguire la compilazione**. Il [compilazione di distribuzione autonoma](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) processo verrà pubblicati gli artefatti che è possono eseguire in Azure e Azure Stack.

**Uso di un agente ospitato di Azure**

Utilizzo di un agente ospitato nelle pipeline di Azure è un'opzione utile per compilare e distribuire le app web. Gli aggiornamenti e manutenzione viene eseguiti automaticamente da Microsoft Azure, consentendo lo sviluppo continuo e ininterrotto, test e alla distribuzione.

### <a name="manage-and-configure-the-cd-process"></a>Gestire e configurare il processo di recapito Continuo

DevOps e Azure DevOps Server di Azure forniscono una pipeline, altamente configurabile e gestibile per le versioni in più ambienti, ad esempio sviluppo, staging, controllo qualità e ambienti di produzione; inclusa la richiesta di approvazione in specifiche fasi.

#### <a name="create-release-definition"></a>Creare una definizione della versione


![Alt text](media/azure-stack-solution-geo-distributed/image5.png)

1.  Selezionare il **plus** per aggiungere una nuova versione sotto il **scheda rilasci** nella pagina di compilazione e versione di Visual Studio Online (VSO).

    ![Alt text](media/azure-stack-solution-geo-distributed/image6.png)

2. Si applicano i **distribuzione di Azure App Service** modello.

    ![Alt text](media/azure-stack-solution-geo-distributed/image7.png)

3. Nel menu a discesa Aggiungi artefatto **aggiungere l'elemento** per l'app di compilazione Cloud di Azure.

    ![Alt text](media/azure-stack-solution-geo-distributed/image8.png)

4. Nella scheda della Pipeline, selezionare la **fase, l'attività** collegamento dell'ambiente e impostare i valori di ambiente cloud di Azure.

    ![Alt text](media/azure-stack-solution-geo-distributed/image9.png)

5. Impostare il **nome dell'ambiente** e selezionare Azure **sottoscrizione** per l'endpoint Cloud di Azure.

    ![Alt text](media/azure-stack-solution-geo-distributed/image10.png)

6. In nome dell'ambiente, impostare la necessaria **nome del servizio app di Azure**.

    ![Alt text](media/azure-stack-solution-geo-distributed/image11.png)

7. Immettere **Hosted VS2017** nella coda dell'agente per l'ambiente cloud di Azure ospitati.

    ![Alt text](media/azure-stack-solution-geo-distributed/image12.png)

8. Distribuzione servizio App di Azure selezionare menu validi **pacchetto o cartella** per l'ambiente. Fare clic su OK per **percorso cartella**.

    ![Alt text](media/azure-stack-solution-geo-distributed/image13.png)

    ![Alt text](media/azure-stack-solution-geo-distributed/image14.png)

9. Salvare tutte le modifiche e tornare alla **pipeline di rilascio**.

    ![Alt text](media/azure-stack-solution-geo-distributed/image15.png)

10. Aggiungere un **nuovo elemento** selezionando la compilazione per l'app di Azure Stack.

    ![Alt text](media/azure-stack-solution-geo-distributed/image16.png)

11. Aggiungere un ambiente più applicando il **distribuzione servizio App di Azure.**

    ![Alt text](media/azure-stack-solution-geo-distributed/image17.png)

12. Denominare il nuovo ambiente **Azure Stack.**

    ![Alt text](media/azure-stack-solution-geo-distributed/image18.png)

13. Trovare l'ambiente Azure Stack sotto **attività** scheda.

    ![Alt text](media/azure-stack-solution-geo-distributed/image19.png)

14. Selezionare il **sottoscrizione** per l'endpoint di Azure Stack.

  ![Alt text](media/azure-stack-solution-geo-distributed/image20.png)

15. Impostare il nome di app web di Azure Stack come le **nome del servizio App**.

    ![Alt text](media/azure-stack-solution-geo-distributed/image21.png)

16. Selezionare il **agente di Azure Stack**.

    ![Alt text](media/azure-stack-solution-geo-distributed/image22.png)

17. In servizio App di Azure di distribuire sezione selezionare validi **pacchetto o cartella** per l'ambiente. Fare clic su OK per **percorso cartella**.

    ![Alt text](media/azure-stack-solution-geo-distributed/image23.png)

    ![Alt text](media/azure-stack-solution-geo-distributed/image24.png)

18. Sotto **variabile** scheda aggiungere una variabile denominata `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS`, impostare il relativo valore come `true`e definire l'ambito per `Azure Stack`.

    ![Alt text](media/azure-stack-solution-geo-distributed/image25.png)

19. Selezionare il **Continuous** icona di trigger di distribuzione in entrambi gli elementi e abilitare le **continua ad** trigger di distribuzione.

    ![Alt text](media/azure-stack-solution-geo-distributed/image26.png)

20. Selezionare il **pre-distribuzione** icona condizioni nell'ambiente Azure Stack e impostare il trigger su **dopo il rilascio.**

21. Salvare tutte le modifiche.

> [!Note]  
>  Alcune impostazioni per le attività possono essere state automaticamente definite come [variabili di ambiente](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables) al momento della creazione di una definizione di versione da un modello. Queste impostazioni non possono essere modificate nelle impostazioni di attività; invece è necessario selezionare l'elemento di ambiente padre per modificare queste impostazioni.

## <a name="part-2-update-web-app-options"></a>Parte 2: Aggiornare le opzioni di app web

[Servizio app di Azure](https://docs.microsoft.com/azure/app-service/overview) offre un servizio di hosting Web con scalabilità elevata e funzioni di auto-correzione. 

![Alt text](media/azure-stack-solution-geo-distributed/image27.png)

> [!div class="checklist"]
> - Esecuzione del mapping di un nome DNS personalizzato esistente con un app Web di Azure
> - Usare un * * registrazione CNAME un' **un record** per eseguire il mapping di un nome DNS personalizzato in servizio App di.

### <a name="map-an-existing-custom-dns-name-to-azure-web-apps"></a>Esecuzione del mapping di un nome DNS personalizzato esistente con un app Web di Azure

> [!Note]  
>  Usare un record CNAME per tutti i nomi DNS personalizzati, ad eccezione di un dominio radice (per example,northwind.com).

Per eseguire la migrazione di un sito in tempo reale e del relativo nome di dominio DNS al servizio app, vedere [Migrare un nome DNS attivo nel servizio app di Azure](https://docs.microsoft.com/azure/app-service/manage-custom-dns-migrate-domain).

### <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

-   [Creare un'app di servizio App](https://docs.microsoft.com/azure/app-service/), oppure usare un'app creata per un'altra esercitazione.

-   Acquistare un nome di dominio e garantire l'accesso al registro DNS per il provider di dominio.

Aggiornare il file di zona DNS per il dominio. Azure AD verificherà la proprietà del nome di dominio personalizzato. Uso [DNS di Azure](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) per i record DNS di Azure/Office 365/esterni in Azure, oppure aggiungere la voce DNS in [un registrar DNS diverso](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

-   Registrare un dominio personalizzato con un Registrar di pubblico.

-   Accedere al registrar per il dominio. (Un amministratore approvato potrebbe essere necessario apportare gli aggiornamenti DNS.)

-   Aggiornare il file di zona DNS per il dominio aggiungendo la voce DNS fornita da Azure AD.

Ad esempio, per aggiungere DNS voci fornorthwindcloud.comand www.northwindcloud.com, configurare le impostazioni DNS per il dominio di radice thenorthwindcloud.com.

> [!Note]  
>  Un nome di dominio può essere acquistato tramite il [portale di Azure](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain).  
> Per eseguire il mapping di un nome DNS personalizzato a un'app Web, è necessario che il [piano di servizio app](https://azure.microsoft.com/pricing/details/app-service/) dell'app Web sia un livello a pagamento (**Condiviso**, **Base**, **Standard** o **Premium**).



### <a name="create-and-map-cname-and-a-records"></a>Creare ed eseguire il mapping di record CNAME e A

#### <a name="access-dns-records-with-domain-provider"></a>Accedere ai record DNS con il provider di dominio

> [!Note]  
>  Usare DNS di Azure per configurare un nome DNS personalizzato per App Web di Azure. Per altre informazioni, vedere [Usare il servizio DNS di Azure per specificare impostazioni di dominio personalizzate per un servizio di Azure](https://docs.microsoft.com/azure/dns/dns-custom-domain).

1.  Accedere al sito Web del provider di principale.

2.  Individuare la pagina relativa alla gestione dei record DNS. Ogni provider di dominio ha la propria interfaccia per i record DNS. Cercare le aree del sito denominate **Domain Name** (Nome di dominio), **DNS** o **Name Server Management** (Gestione server dei nomi).

Pagina dei record DNS può essere visualizzato nella **My domains**. Trovare il collegamento denominato **file di zona**, **i record DNS**, o **configurazione avanzata**.

La schermata seguente è un esempio di pagina di record DNS:

![Pagina record DNS di esempio](media/azure-stack-solution-geo-distributed/image28.png)

1.  Nel Registrar di nomi di dominio, selezionare **Add o Create** per creare un record. Per alcuni provider esistono collegamenti diversi per aggiungere tipi di record diversi. Consultare la documentazione del provider.

2.  Aggiungere un record CNAME per eseguire il mapping di un sottodominio al nome host predefinito dell'app.

  Nell'esempio www.northwindcloud.comdomain, aggiungere un record CNAME che esegue il mapping di namewwwto < app\_name >. azurewebsites.net.

Dopo aver aggiunto il record CNAME, la pagina dei record DNS è simile al seguente:

![Passaggio all'app di Azure nel portale](media/azure-stack-solution-geo-distributed/image29.png)

### <a name="enable-the-cname-record-mapping-in-azure"></a>Abilitare il mapping dei record CNAME in Azure

1.  In una nuova scheda, accedere al portale di Azure

2.  Passare a servizi App.

3.  Selezionare l'app web.

4.  Nel riquadro di spostamento a sinistra della pagina dell'app nel portale di Azure selezionare **Domini personalizzati**.

5.  Selezionare l'icona **+** accanto ad **Aggiungi il nome host**.

1.  Digitare il nome di dominio completo, ad esempio `www.northwindcloud.com`.

2.  Selezionare **Convalida**.

3.  Se indicato, aggiungere altri record di altri tipi (`A` o `TXT`) per i record DNS del Registrar di dominio nome. Azure fornirà i valori e tipi di questi record:

    a.  Un record **A** di cui eseguire il mapping all'indirizzo IP dell'app.

    b.  Oggetto **TXT** record per eseguire il mapping al nome host predefinito dell'app < app_name >. azurewebsites.net. Servizio App Usa questo record solo in fase di configurazione per verificare la proprietà di dominio personalizzato. Dopo la verifica, eliminare il record TXT.

4.  Completare questa attività nella scheda registrar di dominio e convalidare di nuovo finché il **aggiungere il nome host** viene attivato il pulsante.

5.  Verificare che l'opzione * * tipo di record del nome host è impostata su **CNAME (www.example.com o qualsiasi sottodominio)**.

6.  Selezionare **Aggiungi il nome host**.

7.  Digitare il nome di dominio completo, ad esempio `northwindcloud.com`.

8.  Selezionare **Convalida**.

9.  Il **Add** è attivato.

10. Verificare che l'opzione * * tipo di record del nome host è impostata su **record a (esempio.com)**.

11. **Aggiungi il nome host**.

  Potrebbe richiedere qualche minuto per i nuovi nomi host dell'app siano presenti **domini personalizzati** pagina. Provare ad aggiornare il browser per visualizzare i dati più recenti.
  
  ![Alt text](media/azure-stack-solution-geo-distributed/image31.png) 
  
  In caso di errore, verrà visualizzata una notifica di errore di verifica nella parte inferiore della pagina. ![Errore di verifica](media/azure-stack-solution-geo-distributed/image32.png)

> [!Note]  
>  I passaggi precedenti possono essere ripetuti per eseguire il mapping di un dominio con caratteri jolly (\*. northwindcloud.com)... In questo modo l'aggiunta di eventuali sottodomini aggiuntivi per questo servizio app senza dover creare un record CNAME per ognuno di essi. Seguire le istruzioni di programma di registrazione per configurare questa impostazione.

#### <a name="test-in-a-browser"></a>Test in un browser

Individuare i nomi DNS configurati in precedenza (ad esempio, `northwindcloud.com`, www.northwindcloud.com.

## <a name="part-3-bind-a-custom-ssl-cert"></a>Parte 3: Associare un certificato SSL personalizzato

In questa parte:

> [!div class="checklist"]
> - Associare il certificato SSL personalizzato al servizio App
> - Imporre HTTPS per l'app
> - Automatizzare l'associazione dei certificati SSL con gli script

> [!Note]  
> Se necessario, ottenere un cliente il certificato SSL nel portale di Azure e associarlo all'app web. Seguire l'[esercitazione sui certificati del Servizio app](https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site).

### <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

-   [Creare un'app del Servizio app](https://docs.microsoft.com/azure/app-service/)
-   [Eseguire il mapping di un nome DNS personalizzato all'app Web](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain)
-   Acquisire un certificato SSL da un'autorità di certificazione attendibile e usare la chiave per firmare la richiesta

### <a name="requirements-for-your-ssl-certificate"></a>Requisiti per il certificato SSL

Per poter essere usato nel servizio app, il certificato deve soddisfare tutti i requisiti seguenti:

-   Deve essere firmato da un'autorità di certificazione attendibile

-   Deve essere esportato come file PFX protetto da password

-   Deve contenere una chiave privata costituita da almeno 2048 bit

-   Deve contenere tutti i certificati intermedi nella catena di certificati.

> [!Note]  
>  **I certificati ECC (Curve Cryptography) ellittica** funzionano con il servizio App ma non sono inclusi in questa Guida. Per assistenza nella creazione di certificati ECC, consultare un'autorità di certificazione. 

#### <a name="prepare-the-web-app"></a>Preparare l'app web

Per associare un certificato SSL personalizzato all'app web, il [piano di servizio App](https://azure.microsoft.com/pricing/details/app-service/) deve essere il **base**, **Standard**, o **Premium** livello.

#### <a name="sign-in-to-azure"></a>Accedere ad Azure

1.  Aprire il [portale di Azure](https://portal.azure.com/) e passare all'app web.

2.  Nel menu a sinistra, selezionare **servizi App**e quindi selezionare il nome dell'app web.

![Selezionare l'app Web](media/azure-stack-solution-geo-distributed/image33.png)

#### <a name="check-the-pricing-tier"></a>Scegliere il piano tariffario

1.  Nel riquadro di spostamento a sinistra della pagina dell'app web, scorrere fino al **le impostazioni** della sezione e selezionare **scala verticalmente (piano di servizio App)**.

    ![Menu di scalabilità verticale](media/azure-stack-solution-geo-distributed/image34.png)

1.  Verificare che l'app web non è nel **gratuito** oppure **condiviso** livello. Livello corrente dell'app web è evidenziato in una casella blu scuro.

    ![Controllare il piano tariffario](media/azure-stack-solution-geo-distributed/image35.png)

Il certificato SSL personalizzato non è supportato nel livello **Gratuito** o **Condiviso**. Per ingrandire, seguire i passaggi nella sezione successiva, oppure **scegliere il piano tariffario** pagina e andare al [caricare e associare il certificato SSL](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl).

#### <a name="scale-up-your-app-service-plan"></a>Passare a un piano di servizio app superiore

1.  Selezionare uno tra i livelli **Basic**, **Standard** o **Premium**.

2.  Scegliere **Seleziona**.

![Scegliere un piano tariffario](media/azure-stack-solution-geo-distributed/image36.png)

L'operazione di ridimensionamento è completa quando viene visualizzata la notifica.

![Notifica di passaggio al livello superiore](media/azure-stack-solution-geo-distributed/image37.png)

#### <a name="bind-your-ssl-certificate-and-merge-intermediate-certificates"></a>Associare il certificato SSL e unire i certificati intermedi

Unire più certificati nella catena.

1. **Aprire ogni certificato** è stato ricevuto in un editor di testo.

2. Creare un file per il certificato unito denominato *mergedcertificate.crt*. In un editor di testo copiare il contenuto di ogni certificato nel file. L'ordine dei certificati deve corrispondere all'ordine nella catena di certificati, che inizia con il certificato dell'utente e termina con il certificato radice. Sarà simile a quanto indicato nell'esempio seguente:

    ```Text

    -----BEGIN CERTIFICATE-----

    <your entire Base64 encoded SSL certificate>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded intermediate certificate 1>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded intermediate certificate 2>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded root certificate>

    -----END CERTIFICATE-----
    ```

#### <a name="export-certificate-to-pfx"></a>Esportare il certificato in un file PFX

Esportare il certificato SSL unito con la chiave privata generata dal certificato.

Viene creato un file di chiave privato tramite OpenSSL. Per esportare il certificato in un file PFX, eseguire il comando seguente, sostituendo i segnaposto *< private-key-file >* e *< merged-certificate-file >* con i percorsi delle chiavi privati e di merge file di certificato.

```PowerShell
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>
```

Quando richiesto, specificare una password di esportazione per il caricamento del certificato SSL al servizio App in un secondo momento.

Quando IIS oppure **Certreq.exe** vengono usati per generare la richiesta di certificato, installare il certificato in un computer locale e quindi [esportare il certificato in un file PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

#### <a name="upload-the-ssl-certificate"></a>Caricare il certificato SSL

1.  Selezionare **impostazioni SSL** nel riquadro di spostamento a sinistra dell'app web.

2.  Selezionare **caricare il certificato**.

3.  Nelle **File di certificato PFX**, selezionare il file PFX.

4.  4. Nelle **password del certificato**, digitare la password creata durante l'esportazione del file PFX.

5.  Selezionare **Carica**.

![Caricamento del certificato](media/azure-stack-solution-geo-distributed/image38.png)

Dopo che il servizio App ha terminato il caricamento del certificato, viene visualizzata la **impostazioni SSL** pagina.

![Alt text](media/azure-stack-solution-geo-distributed/image39.png)

#### <a name="bind-your-ssl-certificate"></a>Associare il certificato SSL

1.  Nel **associazioni SSL** sezione, selezionare **aggiungere binding**.

    > [!Note]  
    >  Se il certificato è stato caricato, ma non viene visualizzato in nomi di dominio nel **Hostname** elenco a discesa, provare ad aggiornare la pagina del browser.

1.  Nel **Aggiungi associazione SSL** pagina, usare il menu a discesa per selezionare il nome di dominio da proteggere e il certificato da usare.

2.  Nelle **tipo SSL**, scegliere se usare [ **indicazione nome Server (SNI)**](http://en.wikipedia.org/wiki/Server_Name_Indication)o basato su IP SSL.

-   **SSL basato su SNI**-è possibile aggiungere associazioni SSL basate su SNI più. Questa opzione consente di usare più certificati SSL per proteggere più domini nello stesso indirizzo IP. La maggior parte dei browser moderni (tra cui Internet Explorer, Chrome, Firefox e Opera) supporta SNI. Per altre informazioni sul supporto dei browser, vedere [Indicazione nome server](http://wikipedia.org/wiki/Server_Name_Indication).

-   **SSL basato su IP**-può essere aggiunta una sola associazione SSL basata su IP. Questa opzione consente di usare solo un certificato SSL per proteggere un indirizzo IP pubblico dedicato. Per proteggere più domini, proteggerli tutti usando lo stesso certificato SSL. Questa è l'opzione tradizionale per l'associazione SSL.

    1.  Selezionare **aggiungere un'associazione**.

    ![Alt text](media/azure-stack-solution-geo-distributed/image40.png)

Dopo che il servizio App ha terminato il caricamento del certificato, viene visualizzata la **associazioni SSL** sezioni.

![Alt text](media/azure-stack-solution-geo-distributed/image41.png)

#### <a name="remap-the-a-record-for-ip-ssl"></a>Modificare il mapping del record a per IP SSL

Se SSL basato su IP non viene usato nell'app web, andare al [testare HTTPS per il dominio personalizzato](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl).

Per impostazione predefinita, l'app web Usa un indirizzo IP pubblico condiviso. Quando il certificato è associato a SSL basato su IP, servizio App crea un indirizzo IP di nuova e dedicato per l'app web.

Quando viene eseguito il mapping di un record all'app web, il Registro di sistema di dominio deve essere aggiornato con l'indirizzo IP dedicato.

Il **Custom domain** pagina viene aggiornata con il nuovo indirizzo IP dedicato. Copiare questa [indirizzo IP](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain), quindi eseguire nuovamente il mapping di [un record](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain) questo nuovo indirizzo IP.

#### <a name="test-https"></a>Testare HTTPS

In diversi browser passare a https://<your.custom.domain>to assicurarsi web PA viene servita.

![Alt text](media/azure-stack-solution-geo-distributed/image42.png)

> [!Note]  
> Se si verificano errori di convalida del certificato, potrebbe trattarsi di un certificato autofirmato o i certificati intermedi possono avere stati omessi dai durante l'esportazione nel file PFX.

#### <a name="enforce-https"></a>Applicare HTTPS

Per impostazione predefinita, tutti gli utenti possono accedere all'app web tramite HTTP. tutte le richieste HTTP sulla porta HTTPS potrebbero essere reindirizzate.

Nella pagina dell'app web, selezionare **impostazioni SL**. Quindi in **Solo HTTPS**, selezionare **Attiva**.

![Applicare HTTPS](media/azure-stack-solution-geo-distributed/image43.png)

Una volta completata l'operazione, passare a uno degli URL HTTP che puntano all'app. Ad esempio: 

-   http://<APP_NAME>.azurewebsites.NET
-   http://northwindcloud.com
-   <http://www.northwindcloud.com>

#### <a name="enforce-tls-1112"></a>Applicare TLS 1.1/1.2

L'app consente [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.0 per impostazione predefinita, non è più considerato sicuro da standard di settore, ad esempio [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Per applicare versioni di TLS più recenti, seguire questa procedura:

1.  Nella pagina di app web, nel riquadro di spostamento a sinistra, selezionare **impostazioni SSL**.

2.  Nelle **versione TLS**, selezionare la versione minima di TLS.

![Applicare TLS 1.1 o 1.2](media/azure-stack-solution-geo-distributed/image44.png)

### <a name="create-a-traffic-manager-profile"></a>Creare un profilo di Gestione traffico

1.  Selezionare **crea una risorsa** > **Networking** > **profilo di Traffic Manager** > **crea**.

2.  In **Crea profilo di Gestione traffico** procedere come segue:

    1.  Nelle **nome**, specificare un nome per il profilo. Questo nome deve essere univoco all'interno della zona manager.net traffico e i risultati nel nome DNS, il traffico manager.net, che consente di accedere al profilo di Traffic Manager.

    2.  Nelle **metodo di Routing**, selezionare la **metodo Geographicrouting**.

    3.  Nelle **sottoscrizione**, selezionare la sottoscrizione in cui deve essere stato creato il profilo.

    4.  In **Gruppo di risorse** creare un nuovo gruppo di risorse in cui aggiungere il profilo.

    5.  In **Località del gruppo di risorse** selezionare la località del gruppo di risorse. Questa impostazione indica la località del gruppo di risorse e non ha alcun impatto sul profilo di Traffic Manager che verrà distribuito a livello globale.

    6.  Selezionare **Create**.

    7.  Una volta completata la distribuzione globale del profilo di Traffic Manager, viene elencato nel rispettivo gruppo di risorse come una delle risorse.

    ![Alt text](media/azure-stack-solution-geo-distributed/image45.png)

### <a name="add-traffic-manager-endpoints"></a>Aggiungere endpoint di Gestione traffico

1.  Nella barra di ricerca portali, cercare la * * profilo di gestione traffico * * nome creato nella sezione precedente e selezionare il profilo di gestione traffico nei risultati che l'oggetto visualizzato.

2.  In **profilo di gestione traffico**, nella **impostazioni** sezione, selezionare **endpoint**.

3.  Selezionare **Aggiungi**.

4.  Aggiungere l'Endpoint di Azure Stack.

5.  Per la **tipo**, selezionare **endpoint esterno**.

6.  Fornire una **nome** per questo endpoint, idealmente il nome dello Stack di Azure.

7.  Per il nome di dominio completo (**FQDN**), l'URL esterno usare per l'App Web di Azure Stack.

8.  In Geo-mapping, selezionare un continente/area geografica in cui si trova la risorsa, ad esempio, **Europa.**

9.  Selezionare il paese che si applicherà a questo endpoint, ad esempio, il paese/area geografica elenco a discesa che viene visualizzato **Germania**.

10. Mantenere deselezionata l'opzione **Aggiungi come disabilitato**.

11. Selezionare **OK**.

12. Aggiungere l'Endpoint di Azure:

    1.  Per la **tipo**, selezionare **endpoint di Azure**.

    2.  Fornire una **nome** per questo endpoint.

    3.  Per la **tipo di risorsa di destinazione**, selezionare **servizio App**.

    4.  Per la **risorsa di destinazione**, selezionare **scegliere un servizio app** per visualizzare l'elenco delle App Web nella stessa sottoscrizione. Nelle **risorsa**, selezionare l'uso di servizio App come primo endpoint.

13. Selezionare un continente/area geografica in cui si trova la risorsa, ad esempio, mapping geografico **America del Nord America/centrale/Caraibi.**

14. Sotto il paese/area geografica elenco a discesa che viene visualizzato lasciare vuoto questo campo per selezionare tutto il raggruppamento a livello di area precedente.

15. Mantenere deselezionata l'opzione **Aggiungi come disabilitato**.

16. Selezionare **OK**.

  > [!Note]  
  >  Creare almeno un endpoint con un ambito geografico di tutti i (mondiale) da usare come endpoint predefinito per la risorsa.

1.  Una volta completata l'aggiunta di entrambi gli endpoint, essi vengono visualizzati in **Profilo di Gestione traffico** insieme al relativo stato di monitoraggio **Online**.

  ![Alt text](media/azure-stack-solution-geo-distributed/image46.png)

**Azienda globale si basa sulle funzionalità di distribuzione a livello geografico di Azure**

Indirizzare il traffico di dati tramite Gestione traffico di Azure e gli endpoint specifici del geography consente alle aziende globali di rispettare le normative locali e mantenere i dati, conformità e la sicurezza fondamentale per il successo di ufficio locali e in posizioni remote.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sui modelli Cloud di Azure, vedere [Cloud Design Patterns](https://docs.microsoft.com/azure/architecture/patterns).
