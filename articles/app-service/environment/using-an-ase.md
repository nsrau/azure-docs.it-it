---
title: Usare e gestire un ambiente del servizio appUse and manage an App Service Environment
description: Informazioni su come creare, pubblicare e ridimensionare le app in un ambiente del servizio app. Trova tutte le attività comuni in questo articolo.
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 01/01/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 8a73c1998203a8696b67a5e7eb3af23898239265
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477630"
---
# <a name="use-an-app-service-environment"></a>Usare un ambiente del servizio app

Un ambiente del servizio app (AS) è una distribuzione del servizio app di Azure in una subnet nell'istanza di Rete virtuale di Azure di un cliente. Un'ase è costituita da:

- **Front-end**: dove HTTP o HTTPS termina in un ambiente del servizio app.
- **Lavoratori:** le risorse che ospitano le app.
- **Database**: Contiene informazioni che definiscono l'ambiente.
- **Storage**( Archiviazione : Usato per ospitare le app pubblicate dal cliente).

È possibile distribuire un ambiente del servizio app con un indirizzo IP virtuale esterno o interno (VIP) per l'accesso alle app. Una distribuzione con un indirizzo VIP esterno viene comunemente definita *ascora esterno*. Una distribuzione con un indirizzo VIP interno viene definita servizio del servizio *app ILB* perché usa un servizio di bilanciamento del carico interno. Per altre informazioni sull'ambiente del servizio app con bilanciamento del carico interno, vedere [Creazione e uso di un servizio di bilanciamento del carico interno con un ambiente del servizio app][MakeILBASE].

## <a name="create-an-app-in-an-ase"></a>Creare un'app in un ambiente del servizio app

Per creare un'app in un'app, puoi usare lo stesso processo di quando crei normalmente un'app, ma con alcune piccole differenze. Quando si crea un nuovo piano di servizio app:

- Invece di selezionare una località geografica per distribuire l'app, selezionare un ambiente del servizio app come posizione.
- Tutti i piani del servizio app creati in un ambiente del servizio app possono essere solo in un piano tariffario isolato.

Se non si dispone di un ambiente del servizio app, è possibile crearne uno seguendo le istruzioni in Creare un ambiente del [servizio app.][MakeExternalASE]

Per creare un'app in un ambiente del servizio app:

1. Selezionare **Crea una risorsa** > **Web - App** > **Web**mobile .

1. Immettere un nome per l'app. Se hai già selezionato un piano di servizio app in un'app per l'app, il nome di dominio per l'app rifletterà il nome di dominio dell'app:

    ![Selezione del nome per l'app][1]

1. Selezionare una sottoscrizione.

1. Immettere un nome per un nuovo gruppo di risorse, oppure fare clic su **Usa esistente** e selezionarne uno dall'elenco a discesa.

1. Selezionare il sistema operativo.

1. Selezionare un piano di servizio app esistente nell'ambiente del servizio app o crearne uno nuovo con la procedura seguente:

    a. Nel menu a sinistra del portale di Azure selezionare **Crea una risorsa > Web App.**

    b. Selezionare la sottoscrizione.

    c. Selezionare o creare il gruppo di risorse.

    d. Immettere il nome dell'app Web.

    e. Selezionare **Code** o **DockerContainer**.

    f. Selezionare uno stack di runtime.

    g. Selezionare **Linux** o **Windows**. 

    h. Selezionare l'ase nell'elenco a discesa **Regione.** 

    i. Selezionare o creare un nuovo piano di servizio app. Se si crea un nuovo piano di servizio app, selezionare la dimensione sKU **isolata** appropriata.

    ![Piani tariffari isolati][2]

    > [!NOTE]
    > Le app Linux e Windows non possono essere nello stesso piano di servizio app, ma possono trovarsi nello stesso ambiente del servizio app.
    >

1. Selezionare **Revisione e crea**, assicurarsi che le informazioni siano corrette e quindi selezionare **Crea**.

## <a name="how-scale-works"></a>Come funziona il ridimensionamento

Ogni app del servizio app viene eseguita in un piano di servizio app. Gli ambienti del servizio app di Azure contengono piani del servizio app, che a loro volta contengono app. Quando si ridimensiona un'app, si ridimensiona anche il piano di servizio app e tutte le app nello stesso piano.

Quando si ridimensiona un piano di servizio app, l'infrastruttura necessaria viene aggiunta automaticamente. Si è verificato un ritardo nella scalabilità delle operazioni durante l'aggiunta dell'infrastruttura. Se si eseguono diverse operazioni di scalabilità in sequenza, viene eseguita la prima richiesta di scalabilità dell'infrastruttura e le altre vengono accodate. Al termine della prima operazione di scalabilità, le altre richieste di infrastruttura interagiscono tutte. E quando viene aggiunta l'infrastruttura, i piani di servizio app vengono assegnati in base alle esigenze. La creazione di un nuovo piano di servizio app è a sua volta un'operazione di scalabilità perché richiede hardware aggiuntivo.

Nel servizio app multi-tenant, la scalabilità è immediata perché un pool di risorse è prontamente disponibile per supportarlo. In un'app, non esiste tale buffer e le risorse vengono allocate in base alle esigenze.

In un'app, è possibile ridimensionare un piano di servizio app fino a 100 istanze. Un servizio app può avere fino a 201 istanze totali in tutti i piani del servizio app in tale app.

## <a name="ip-addresses"></a>Indirizzi IP

Il servizio app può allocare un indirizzo IP dedicato a un'app. Questa funzionalità è disponibile dopo aver configurato SSL basato su IP, come descritto in [Bind an existing custom TLS/SSL certificate to Azure App Service][ConfigureSSL]. In un'associazione del lavoro in un'associazione all'altro, non è possibile aggiungere altri indirizzi IP da utilizzare per SSL basato su IP.

Con un ambiente del servizio app esterno, puoi configurare SSL basato su IP per la tua app nello stesso modo del servizio app multi-tenant. C'è sempre un indirizzo di riserva nell'asino, fino a 30 indirizzi IP. Ogni volta che ne usi uno, un altro viene aggiunto in modo che un indirizzo sia sempre prontamente disponibile. È necessario un ritardo per allocare un altro indirizzo IP. Questo ritardo impedisce l'aggiunta di indirizzi IP in rapida successione.

## <a name="front-end-scaling"></a>Scalabilità front-end

Quando si aumentano le scalabilità orizzontale dei piani del servizio app, i ruoli di lavoro vengono aggiunti automaticamente per supportarli. Ogni ambiente del servizio app viene creato con due front-end. I front-end vengono ridimensionati automaticamente a una velocità di un front-end per ogni set di 15 istanze del piano di servizio app. Ad esempio, se si dispone di tre piani di servizio app con cinque istanze ciascuno, si avrebbe un totale di 15 istanze e tre front-end. Se si scala a un totale di 30 istanze, si hanno quattro front-end. Questo modello continua durante la scalabilità orizzontale.

Il numero di front-end allocati per impostazione predefinita è valido per un carico moderato. È possibile ridurre il rapporto a un minimo di un front-end per ogni cinque istanze. È inoltre possibile modificare le dimensioni dei front-end. Per impostazione predefinita, sono single core. Nel portale di Azure è invece possibile modificarne le dimensioni in due o quattro core.

C'è un costo per cambiare il rapporto o le dimensioni del front-end. Per altre informazioni, vedere [Prezzi del Servizio app di Azure][Pricing]. Se si desidera migliorare la capacità di carico dell'ambiente del servizio di analisi, si otterrà un maggiore miglioramento con il ridimensionamento ai front-end a due core prima di regolare il rapporto di scala. La modifica delle dimensioni di base del front-end causerà un aggiornamento dell'ase e dovrebbe essere eseguita al di fuori del normale orario di ufficio.

Le risorse front-end sono gli endpoint HTTP/HTTPS per l'ambiente del servizio app. Con la configurazione front-end predefinita, l'uso della memoria per ogni front-end è di circa il 60%. Il motivo principale per ridimensionare i front-end è l'utilizzo della CPU, che è principalmente guidato dal traffico HTTPS.

## <a name="app-access"></a>Accesso all'app

In un'app esterna, il suffisso di dominio usato per la creazione dell'app è *.&lt; asename&gt;.p.azurewebsites.net*. Se l'ase è denominato external-ase e si ospita un'app denominata contoso in tale app, è possibile contattarla in corrispondenza degli URL seguenti:If your ASE is named _external-ase_ and you host an app called _contoso_ in that ASE, you reach it at these URLs:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

Per informazioni su come creare un ambiente del servizio app esterno, vedere Creare un ambiente del [servizio app.][MakeExternalASE]

In un servizio app ILB, il suffisso di dominio utilizzato per la creazione dell'app è *.&lt; asename&gt;.appserviceenvironment.net*. Se l'app ase è denominata _ilb-ase_ e si ospita un'app denominata _contoso_ in tale app, è possibile contattarla in questi URL:

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

Per informazioni su come creare un'impostazione del gruppo di controllo del gruppo del linguaggio intermedio, vedere [Creare e utilizzare un'impostazione del][MakeILBASE]gruppo del gruppo del gruppo del linguaggio scope .

L'URL di Gestione controllo servizi viene usato per accedere alla console Kudu o per pubblicare l'app tramite Distribuzione Web. Per informazioni sulla console Kudu, vedere [Kudu console for Azure App Service][Kudu] (Console Kudu per il servizio app di Azure). La console Kudu offre un'interfaccia utente Web per il debug, il caricamento di file, la modifica di file e altro ancora.

## <a name="publishing"></a>Pubblicazione

In un ambiente del servizio app, come con il servizio app multi-tenant, è possibile pubblicare con questi metodi:In an ASE, as with the multitenant App Service, you can publish by these methods:

- Distribuzione Web
- FTP
- Integrazione continua (CI)
- Trascinamento della selezione nella console Kudu
- Un IDE, ad esempio Visual Studio, Eclipse o IntelliJ IDEA

Con un'app esterna, queste opzioni di pubblicazione funzionano tutte allo stesso modo. Per altre informazioni, vedere [Distribuzione nel servizio app di Azure][AppDeploy].

La pubblicazione è significativamente diversa con un servizio app ILB, per il quale gli endpoint di pubblicazione sono tutti disponibili solo tramite ILB. Il servizio di bilanciamento del carico interno è in un IP privato nella subnet dell'ambiente del servizio app nella rete virtuale. Se non si dispone dell'accesso di rete al bilanciamento del servizio app, non è possibile pubblicare app in tale app. Come indicato in Creare e usare un servizio app [ILB][MakeILBASE], è necessario configurare DNS per le app nel sistema. Tale requisito include l'endpoint di Gestione controllo servizi. Se gli endpoint non sono definiti correttamente, non è possibile pubblicarli. Gli IDE devono inoltre disporre dell'accesso di rete al bilanciamento del servizio ilB per pubblicarlo direttamente.

Senza ulteriori modifiche, i sistemi di integrità basati su Internet come GitHub e DevOps di Azure non funzionano con un ambiente del servizio app ILB perché l'endpoint di pubblicazione non è accessibile a Internet.Senza additional changes, internet-based CI systems like GitHub and Azure DevOps don't work with an ILB ASE because the publishing endpoint isn't internet accessible. È possibile abilitare la pubblicazione in un ambiente del servizio app ILB da Azure DevOps installando un agente di rilascio self-hosted nella rete virtuale che contiene l'ambiente del servizio app ILB. In alternativa, puoi anche utilizzare un sistema CI che usa un modello pull, ad esempio Dropbox.

Gli endpoint di pubblicazione per le app in un ambiente del servizio app con bilanciamento del carico interno usano il dominio con cui l'ambiente del servizio app con bilanciamento del carico interno è stato creato, È possibile visualizzarlo nel profilo di pubblicazione dell'app e nel riquadro del portale dell'app (in **Informazioni di essentiali e** > **Essentials** anche in **Proprietà).**

## <a name="storage"></a>Archiviazione

Un servizio app dispone di 1 TB di spazio di archiviazione per tutte le app nell'app. Un piano di servizio app nello SKU di prezzi isolato ha un limite di 250 GB per impostazione predefinita. Se si dispone di cinque o più piani del servizio app, fare attenzione a non superare il limite di 1 TB dell'app. Se è necessario un limite superiore a 250 GB in un piano di servizio app, contattare il supporto tecnico per modificare il limite del piano di servizio app a un massimo di 1 TB. Quando il limite del piano viene modificato, è ancora previsto un limite di 1 TB in tutti i piani del servizio app nell'app.

## <a name="logging"></a>Registrazione

È possibile integrare l'ambiente del servizio app con Monitoraggio di Azure per inviare log sull'ambiente del servizio app ad Archiviazione di Azure, Hub eventi di Azure o Log Analytics.You can integrate your ASE with Azure Monitor to send logs about the ASE to Azure Storage, Azure Event Hubs, or Log Analytics. Questi elementi vengono registrati oggi:

| Situazione | Message |
|---------|----------|
| AsE non è integro | L'ambiente del servizio del servizio ambiente del database ambiente specificato non è integro a causa di una configurazione di rete virtuale non valida. L'ambiente del servizio del servizio del servizio del servizio del servizio del qui verrà sospeso se lo stato non integro continua. Assicurarsi che le linee https://docs.microsoft.com/azure/app-service/environment/network-infoguida qui definite siano seguite: . |
| La subnet dell'asE è quasi esaurita nello spazio | L'app specificato si trova in una subnet che è quasi esaurita nello spazio. Ci {0} sono indirizzi rimanenti. Una volta esauriti questi indirizzi, l'insieme di interoperabilità orizzontale non sarà in grado di scalare.  |
| AsE si sta avvicinando al limite totale dell'istanza | L'ase specificato si sta avvicinando al limite di istanza totale dell'app. Attualmente contiene {0} istanze del piano di servizio app di un massimo di 201 istanze. |
| L'asE non è in grado di raggiungere una dipendenza | L'ase specificato non {0}è in grado di raggiungere .  Assicurarsi che le linee https://docs.microsoft.com/azure/app-service/environment/network-infoguida qui definite siano seguite: . |
| L'asE è sospeso | L'asino specificato è sospeso. La sospensione dell'ambiente del servizio del servizio utente può essere dovuta a una carenza di account o a una configurazione di rete virtuale non valida. Risolvere la causa principale e riprendere l'ambiente del servizio del servizio del dominio del lavoro per continuare a gestire il traffico. |
| L'aggiornamento dell'ase è stato avviato | È stato avviato un aggiornamento della piattaforma all'ase specificato. Prevedere ritardi nelle operazioni di ridimensionamento. |
| L'aggiornamento dell'ase è stato completato | Un aggiornamento della piattaforma all'ase specificato è stato completato. |
| Le operazioni di scala sono state avviate | Un piano di{0}servizio app ( ) ha iniziato il ridimensionamento. Stato {1} desiderato:{2} I worker.
| Le operazioni di scala sono state completate | Un piano di{0}servizio app ( ) ha terminato il ridimensionamento. Stato {1} attuale:{2} I lavoratori. |
| Le operazioni di scala non sono riuscite | Un piano di{0}servizio app ( ) non è stato ridimensionato. Stato {1} attuale:{2} I lavoratori. |

Per abilitare la registrazione nell'app:

1. Nel portale passare a **Impostazioni di diagnostica**.
1. Selezionare **Aggiungi impostazione diagnostica**.
1. Specificare un nome per l'integrazione del log.
1. Selezionare e configurare le destinazioni di log desiderate.
1. Selezionare **AppServiceEnvironmentPlatformLogs**.

![Impostazioni del log di diagnostica di AsE][4]

Se si esegue l'integrazione con Log Analytics, è possibile visualizzare i log selezionando **Log** dal portale dell'ambiente del servizio app e creando una query su **AppServiceEnvironmentPlatformLogs**.

## <a name="upgrade-preference"></a>Preferenza di aggiornamento

Se si dispone di più del tutto l'utente, è possibile aggiornare alcuni server del tutto il testo prima di altri. All'interno dell'oggetto **Gestione risorse HostingEnvironment,** è possibile impostare un valore per **upgradePreference**. L'impostazione **upgradePreference** può essere configurata utilizzando https://resources.azure.comun modello, ARMClient o . I tre valori possibili sono:

- **Nessuno:** Azure aggiornerà l'ambiente del servizio app in nessun batch specifico. Questo è il valore predefinito.
- **Presto**: l'app verrà aggiornato nella prima metà degli aggiornamenti del servizio app.
- **In ritardo:** l'app verrà aggiornato nella seconda metà degli aggiornamenti del servizio app.

Se si utilizza https://resources.azure.com, attenersi alla seguente procedura per impostare il valore **upgradePreferences:**

1. Passare a resources.azure.com e accedere con l'account Azure.Go to create and sign in with your Azure account.
1. Passare attraverso le\/\[risorse\]\/per\/\[le sottoscrizioni\]\/\/nome sottoscrizione\/resourceGroups nome gruppo di risorse provider di nomi Microsoft.Web hostingEnvironments\/\[nome\]dell'ambiente del database dell'ambiente del database ambiente del database ambiente.
1. Selezionare **Lettura/Scrittura** nella parte superiore.
1. Selezionare **Modifica**.
1. Impostare **upgradePreference** su uno dei tre valori desiderati.
1. Selezionare **Patch**.

![risorse azure com visualizzare][5]

La funzionalità **upgradePreferences** ha più senso quando si dispone di più del posto di tutto il mondo del lavoro perché gli ase "Early" verranno aggiornati prima degli ase "Late". Quando si dispone di più ambienti del tutto l'ambiente del lavoro, è necessario impostare lo sviluppo e testare gli ambienti del gruppo di controllo dell'ambiente del lavoro su "Early" e gli ambienti del gruppo di ambiente di produzione come "Late".

## <a name="pricing"></a>Prezzi

Lo SKU dei prezzi denominato *Isolato* è utilizzabile solo con gli ambiente del servizio ilcom a serve. Tutti i piani del servizio app ospitati nell'ambiente del servizio app si trovano nello SKU dei prezzi isolato. Le tariffe isolate per i piani del servizio app possono variare in base all'area geografica.

Oltre al prezzo dei piani del servizio app, è disponibile una tariffa fissa per l'app stessa. La tariffa forfettaria non cambia con le dimensioni dell'app. Paga l'infrastruttura dell'app a una velocità di scalabilità predefinita di un front-end aggiuntivo per ogni 15 istanze del piano di servizio app.

Se la frequenza di scalabilità predefinita di un front-end ogni 15 istanze del piano di servizio app non è sufficientemente veloce, è possibile regolare il rapporto con cui vengono aggiunti i front-end o le dimensioni dei front-end. Quando si modificano il rapporto o le dimensioni, si paga per i core front-end che non verrebbero aggiunti per impostazione predefinita.

Se ad esempio si imposta la proporzione di ridimensionamento su 10, verrà aggiunto un front-end ogni 10 istanze nei piani di servizio app. La tariffa fissa copre una proporzione di ridimensionamento pari a un front-end ogni 15 istanze. Con una proporzione di ridimensionamento di 10, il costo verrà addebitato al terzo front-end aggiunto per le 10 istanze del piano di servizio app. Non è necessario pagare quando si raggiungono le 15 istanze, perché il front-end è stato aggiunto automaticamente.

Se si regola la dimensione dei front-end a due core ma non si regola il rapporto, si paga per i core aggiuntivi. Un servizio del servizio interno viene creato con due front-end, quindi anche al di sotto della soglia di ridimensionamento automatico si pagherebbe per due core aggiuntivi se si aumentasse la dimensione a due core front-end.

Per altre informazioni, vedere [Prezzi del Servizio app di Azure][Pricing].

## <a name="delete-an-ase"></a>Eliminare un ambiente del servizio app

Per eliminare un ambiente del servizio app:

1. Selezionare **Elimina** nella parte superiore del riquadro **Ambiente del servizio app.**

1. Immettere il nome dell'ambiente del servizio app per confermare l'eliminazione. Quando elimini un'area del database dell'entità del bene del database, elimini anche tutto il contenuto al suo interno.

    ![Eliminazione dell'ambiente del servizio app][3]

1. Selezionare **OK**.

<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png
[4]: ./media/using_an_app_service_environment/usingase-logsetup.png
[4]: ./media/using_an_app_service_environment/usingase-logs.png
[5]: ./media/using_an_app_service_environment/usingase-upgradepref.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
