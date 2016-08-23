<properties
  pageTitle="Azure IoT Suite e Azure Active Directory | Microsoft Azure"
  description="Descrive in che modo Azure IoT Suite usa Azure Active Directory per gestire le autorizzazioni."
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="05/16/2016"
  ms.author="araguila"/>
  
# Autorizzazioni per il sito azureiotsuite.com

## Cosa accade quando si accede

Quando si accede per la prima volta a [azureiotsuite.com][lnk-azureiotsuite], il sito determina i livelli di autorizzazione in base al tenant di Azure Active Directory (AAD) attualmente selezionato e alla sottoscrizione di Azure.

1.  Il sito cerca prima in Azure a quale tenant di AAD appartiene l'utente per popolare l'elenco di tenant visualizzato accanto al nome utente connesso. A questo punto, il sito può ricevere solo i token utente per un tenant alla volta. Di conseguenza, quando si passa a un tenant diverso usando l'elenco a discesa nell'angolo superiore destro, il sito registra di nuovo l'utente nel tenant per ottenere il token di tale tenant.

2.  Successivamente, il sito cerca in Azure quali sottoscrizioni sono state associate dall'utente al tenant selezionato. Le sottoscrizioni disponibili vengono visualizzate quando si crea una nuova soluzione preconfigurata.

3.  Infine, il sito recupera tutte le risorse nelle sottoscrizioni e nei gruppi di risorse contrassegnati come soluzioni preconfigurate e popola i riquadri nella home page.

Le sezioni seguenti descrivono i ruoli che controllano l'accesso alle soluzioni preconfigurate.

## Ruoli AAD

I ruoli AAD controllano la capacità di provisioning delle soluzioni preconfigurate e gestiscono gli utenti in una soluzione preconfigurata.

Per altre informazioni sui ruoli di amministratore in AAD, vedere [Assegnazione dei ruoli di amministratore in Azure AD][lnk-aad-admin]. Questo articolo è tuttavia incentrato principalmente sui ruoli **Amministratore globale** e **Utente/membro di dominio** usati nelle soluzioni preconfigurate.

**Amministratore globale:** in un tenant di AAD possono essere presenti molti amministratori globali. Quando si crea un tenant di AAD, si è per impostazione predefinita l'amministratore globale del tenant. L'amministratore globale può eseguire il provisioning di una soluzione preconfigurata e gli viene assegnato un ruolo **AMMINISTRATORE** per l'applicazione all'interno di tenant di AAD. Tuttavia, se un altro utente nello stesso tenant di AAD crea un'applicazione, il ruolo predefinito concesso all'amministratore globale è **SOLA LETTURA IMPLICITO**. Gli amministratori globali possono assegnare ruoli per le applicazioni tramite il [portale di Azure classico][lnk-classic-portal].

**Utente/membro di dominio:** per ogni tenant di AAD possono essere presenti molti utenti o membri del dominio. Un utente di dominio può effettuare il provisioning di una soluzione preconfigurata tramite il sito [azureiotsuite.com][lnk-azureiotsuite]. Il ruolo predefinito concesso per l'applicazione di cui effettuano il provisioning è **AMMINISTRATORE**. Possono creare un'applicazione usando lo script build.cmd nel repository [azure-iot-remote-monitoring][lnk-rm-github-repo] o [azure-iot-predictive-maintenance][lnk-pm-github-repo], ma il ruolo predefinito concesso è **IMPLICIT READONLY** (SOLA LETTURA IMPLICITO) perché non hanno l'autorizzazione per assegnare i ruoli. Se un altro utente nel tenant di AAD crea un'applicazione, gli verrà assegnato il ruolo **SOLA LETTURA IMPLICITO** per impostazione predefinita per l'applicazione. Non hanno la possibilità di assegnare ruoli per le applicazioni, quindi non possono aggiungere utenti o ruoli per gli utenti per un'applicazione anche se ne hanno effettuato il provisioning.

**Utente guest/Guest:** per ogni tenant AAD possono essere presenti molti utenti guest o guest. Gli utenti guest hanno un set di diritti limitato nel tenant di AAD. Di conseguenza, gli utenti guest non possono effettuare il provisioning di una soluzione preconfigurata nel tenant di AAD.

Per altre informazioni, vedere le seguenti risorse:

- [Creare o modificare utenti in Azure AD][lnk-create-edit-users]
- [Assegnare i ruoli app in AAD][lnk-assign-app-roles]

## Ruoli di amministratore della sottoscrizione di Azure

I ruoli di amministrazione di Azure controllano la possibilità di eseguire il mapping di una sottoscrizione di Azure a un tenant di AD.

È possibile trovare informazioni sui ruoli Coamministratore, Amministratore del servizio e Amministratore account di Azure nell'articolo [Come aggiungere o modificare i ruoli Coamministratore, Amministratore del servizio e Amministratore account di Azure][lnk-admin-roles].

## Ruoli applicazione

I ruoli applicazione controllano l'accesso ai dispositivi nella soluzione preconfigurato.

Nell'applicazione sono presenti due ruoli definiti e un ruolo implicito creati quando si effettua il provisioning di una soluzione preconfigurata.

-   **AMMINISTRATORE:** ha il controllo completo per aggiungere, gestire e rimuovere dispositivi.

-   **SOLA LETTURA:** ha la possibilità di visualizzare i dispositivi.

-   **SOLA LETTURA IMPLICITO:** è lo stesso di Sola lettura, ma è concesso a tutti gli utenti del tenant di AAD. Questa impostazione è stata aggiunta per motivi di praticità durante lo sviluppo. È possibile rimuovere questo ruolo modificando il file di origine [RolePermissions.cs][lnk-resource-cs].

### Modifica dei ruoli applicazione di un utente

È possibile usare la procedura seguente per configurare un utente di Active Directory come amministratore della soluzione preconfigurata.

Per modificare i ruoli per un utente, è necessario essere un amministratore globale di AAD:

1. Passare al [portale di Azure classico][lnk-classic-portal].

2. Selezionare **Active Directory**.

3. Fare clic sul nome del tenant AAD, ovvero la directory selezionata in azureiotsuite.com durante il provisioning della soluzione.

4. Fare clic su **Applicazioni**.

5. Fare clic sul nome dell'applicazione che coincide con il nome della soluzione preconfigurata. Se l'applicazione non viene visualizzata nell'elenco, nell'elenco a discesa **Mostra** selezionare **Applicazioni di proprietà dell'azienda** e fare clic sul segno di spunta.

7. Fare clic su **Utenti**.

8. Selezionare l'utente per cui si vogliono scambiare i ruoli.

9. Fare clic su **Assegna** e selezionare il ruolo, ad esempio **Amministratore**, da assegnare all'utente, fare clic sul segno di spunta.

## Domande frequenti

### Un amministratore del servizio vuole modificare il mapping della directory tra la sottoscrizione e un tenant di AAD specifico. Come deve procedere?

1. Accedere al [portale di Azure classico][lnk-classic-portal], fare clic su **Impostazioni** nell'elenco dei servizi sul lato sinistro.

2. Selezionare la sottoscrizione per cui si vuole modificare il mapping della directory.

3. Fare clic su **Modifica directory**.

4. Nell'elenco a discesa selezionare la **Directory** si vuole usare. Fare clic sulla freccia Avanti.

5. Verificare il mapping della directory e i coamministratori interessati. Notare che se ci si sposta da un'altra directory, tutti i coamministratori dalla directory originale verranno rimossi.

### Un utente o membro di dominio del tenant di AAD ha creato una soluzione preconfigurata. Come gli viene assegnato un ruolo per l'applicazione?

L'utente deve chiedere a un amministratore globale di assegnargli un ruolo di amministratore globale nel tenant di AAD per ottenere le autorizzazioni per assegnare ruoli agli utenti oppure chiedere a un amministratore globale di assegnargli un ruolo. Se si vuole modificare il tenant di AAD in cui è stata distribuita la soluzione preconfigurata, vedere la domanda successiva.

### Come si cambia il tenant AAD a cui sono assegnate l'applicazione e soluzione preconfigurata di monitoraggio remoto?

È possibile eseguire una distribuzione cloud da <https://github.com/Azure/azure-iot-remote-monitoring> e ridistribuire un tenant di AAD appena creato. Poiché per impostazione predefinita quando si crea un nuovo tenant di AAD si diventa un amministratore globale, si avrà accesso per aggiungere utenti e assegnare ruoli agli utenti.

1. Creare una nuova directory di AAD nel [portale di gestione di Azure][lnk-classic-portal].

2. Passare a <https://github.com/Azure/azure-iot-remote-monitoring>.

3. Eseguire `build.cmd cloud [debug | release] {name of previously deployed remote monitoring solution}`. Ad esempio `build.cmd cloud debug myRMSolution`.

4. Quando richiesto, impostare il **tenantid** come tenant appena creato al posto del tenant precedente.


### Si vuole modificare un amministratore del servizio o coamministratore quando si accede con un account dell'organizzazione

Vedere l'articolo di supporto [Modifica dell'amministratore del servizio e del coamministratore quando si effettua l'accesso con un account dell'organizzazione][lnk-service-admins].

### Perché viene visualizzato un errore simile al seguente? "L'account non ha le autorizzazioni appropriate per creare una soluzione. Rivolgersi all'amministratore account o provare con un account diverso".

Esaminare il diagramma seguente:

![][img-flowchart]

> [AZURE.NOTE] Se si continua a visualizzare l'errore dopo aver confermato di essere un amministratore globale del tenant AAD e un coamministratore della sottoscrizione, l'amministratore dell'account dovrà rimuovere l'utente e assegnare nuovamente le autorizzazioni necessarie in questo ordine: aggiungere l'utente come amministratore globale e quindi aggiungere l'utente come coamministratore della sottoscrizione di Azure. Se i problemi persistono, accedere a [Guida e supporto][lnk-help-support].

**Perché viene visualizzato questo errore quando si ha una sottoscrizione di Azure?** *Per creare soluzioni preconfigurate è necessaria una sottoscrizione di Azure. È possibile creare un account di valutazione gratuito in pochi minuti.*

Se si è certi di che avere una sottoscrizione di Azure, convalidare il mapping del tenant per la sottoscrizione e verificare che sia selezionato il tenant corretto nell'elenco a discesa. Se si è verificato che il tenant desiderato è corretto, seguire il diagramma precedente e verificare il mapping della sottoscrizione e il tenant di AAD.

## Passaggi successivi

Per altre informazioni su IoT Suite, vedere come [personalizzare una soluzione preconfigurata][lnk-customize].

[img-flowchart]: media/iot-suite-permissions/flowchart.png

[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-rm-github-repo]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-aad-admin]: https://azure.microsoft.com/documentation/articles/active-directory-assign-admin-roles/
[lnk-classic-portal]: https://manage.windowsazure.com/
[lnk-create-edit-users]: https://azure.microsoft.com/documentation/articles/active-directory-create-users/
[lnk-assign-app-roles]: https://azure.microsoft.com/documentation/articles/active-directory-application-manifest/
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin/
[lnk-admin-roles]: https://azure.microsoft.com/documentation/articles/billing-add-change-azure-subscription-administrator/
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md

<!---HONumber=AcomDC_0810_2016-->