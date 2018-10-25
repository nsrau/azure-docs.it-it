---
title: Gestire un'applicazione Azure IoT Central | Microsoft Docs
description: Informazioni sulla gestione di applicazioni Azure IoT Central destinate agli amministratori
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 1bb0bc0aa7ad6bbbad502832ba8e0a96f36de428
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/04/2018
ms.locfileid: "48268304"
---
# <a name="administer-your-iot-central-application"></a>Gestire l'applicazione IOT Central

Dopo aver creato un'applicazione Microsoft Azure IoT Central, è possibile usare la sezione **Administration** (Gestione) dell'interfaccia utente di Azure IoT Central per gestirla. Per passare alla sezione **Amministrazione**, scegliere **Amministrazione** nel menu di spostamento a sinistra.

La sezione **Administration** (Gestione) consente di:

- Gestire gli utenti

- Gestire i ruoli

- Visualizzare le informazioni di fatturazione

- Gestire le impostazioni dell'applicazione

- Offrire una versione di valutazione gratuita

Nella sezione **Amministrazione** è presente un menu di spostamento secondario con collegamenti alle varie attività di gestione.

Per accedere alla sezione **Amministrazione** e usarla, è necessario disporre del ruolo **Amministratore** per l'applicazione Azure IoT Central. All'utente che crea un'applicazione Azure IoT Central viene automaticamente assegnato il ruolo **Amministratore** per l'applicazione. La sezione *Gestire gli utenti* più avanti nell'articolo include altre informazioni su come assegnare il ruolo **Administrator** (Amministratore) ad altri utenti.

## <a name="change-application-name"></a>Modificare il nome dell'applicazione

Per modificare il nome dell'applicazione, usare il menu di spostamento secondario per passare alla pagina **Impostazioni applicazione** nella sezione **Amministrazione**.

Nella pagina **Impostazioni applicazione** immettere un nome di propria scelta nel campo **Nome applicazione**. Selezionare quindi **Salva**.

## <a name="change-the-application-url"></a>Modificare l'URL dell'applicazione

Per modificare l'URL dell'applicazione, usare il menu di spostamento secondario per passare alla pagina **Application Settings** (Impostazioni applicazione) nella sezione **Administration** (Amministrazione).

![Pagina delle impostazioni dell'applicazione](media\howto-administer\image0-a.png)

Nella pagina **Impostazioni applicazione** immettere l'URL di propria scelta nel campo **URL** e quindi selezionare **Salva**. L'URL deve avere una lunghezza massima di 200 caratteri. Se l'URL non è disponibile, viene visualizzato un errore di convalida.

> [!Note]
> Se si modifica l'URL, l'URL precedente può essere usato da un altro cliente di Azure IoT Central. In tal caso non sarà più disponibile. L'URL precedente a questo punto non funziona più ed è necessario indicare agli utenti quello nuovo da usare.

## <a name="change-the-application-image"></a>Modificare l'immagine dell'applicazione

Per altre informazioni sull'uso di immagini in un'applicazione Azure IoT Central, vedere [Prepare and upload images to your Azure IoT Central application](howto-prepare-images.md) (Preparare e caricare immagini nell'applicazione Azure IoT Central).

## <a name="copy-an-application"></a>Copiare un'applicazione

È possibile creare una copia di una qualsiasi applicazione, senza tuttavia includere eventuali istanze di dispositivi, la cronologia dei dati dei dispositivi e i dati degli utenti. La copia sarà un'applicazione a pagamento per la quale verrà applicato un addebito. Non è possibile creare un'applicazione di prova copiando un'altra applicazione.

Per copiare un'applicazione, passare alla pagina **Impostazioni applicazione**. Selezionare quindi il pulsante **Copia**.

![Pagina delle impostazioni dell'applicazione](media\howto-administer\appCopy1.png)

Quando si fa clic sul pulsante **Copia**, viene visualizzata una finestra di dialogo in cui è possibile selezionare un nome, un URL, una directory Azure AD, una sottoscrizione e un'area di Azure per la nuova applicazione che verrà creata tramite la copia dell'applicazione originale. Selezionare i valori per ognuno di questi campi. Selezionare quindi il pulsante **Copia** per confermare che si vuole procedere. Nell'articolo che illustra [come creare un'applicazione](howto-create-application.md) sono disponibili maggiori informazioni sui valori da inserire.

![Pagina delle impostazioni dell'applicazione](media\howto-administer\appCopy2.png)

Dopo avere completato la copia dell'app, è possibile passare alla nuova applicazione creata. Per passare all'app, selezionare il collegamento presente nella pagina **Impostazioni applicazione**.

![Pagina delle impostazioni dell'applicazione](media\howto-administer\appCopy3.png)

> [!Note]
> Quando si copia un'applicazione, viene copiata anche la definizione di regole o azioni. Poiché gli utenti che hanno accesso all'app originale non vengono copiati nell'app copiata, sarà necessario aggiungerli manualmente alle azioni per le quali gli utenti sono un prerequisito, ad esempio la posta elettronica.

## <a name="delete-an-application"></a>Eliminare un'applicazione

Per modificare l'applicazione, usare il menu di spostamento secondario per passare alla pagina **Application Settings** (Impostazioni applicazione) nella sezione **Administration** (Amministrazione).

Scegliere **Elimina**.

> [!Note]
> Quando si elimina un'applicazione in modo permanente, vengono eliminati tutti i dati ad essa associati.  Per eliminare un'applicazione, è necessario disporre anche delle autorizzazioni per eliminare risorse nella sottoscrizione di Azure scelta al momento della creazione dell'applicazione. Per altre informazioni, vedere [Usare il controllo degli accessi in base al ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

## <a name="roles-in-azure-iot-central"></a>Ruoli in Azure IoT Central

I ruoli consentono di controllare quali utenti all'interno dell'organizzazione possono eseguire varie attività in Azure IoT Central. In Azure IoT Central sono disponibili tre ruoli che è possibile assegnare agli utenti dell'applicazione. I ruoli vengono assegnati da ogni applicazione. Lo stesso utente può avere ruoli diversi in applicazioni diverse. È possibile assegnare allo stesso utente più ruoli all'interno di un'applicazione.

### <a name="administrator"></a>Amministratore

Gli utenti con ruolo **Administrator** (Amministratore) hanno accesso a tutte le funzionalità in un'applicazione Azure IoT Central.

All'utente che ha creato un'applicazione viene assegnato automaticamente il ruolo **Amministratore**. Ci deve sempre essere almeno un utente con il ruolo **Administrator** (Amministratore).

### <a name="application-builder"></a>Application Builder (Generatore applicazioni)

Gli utenti che hanno il ruolo **Application Builder** possono eseguire tutte le operazioni in un'applicazione Azure IoT Central ad eccezione della gestione dell'applicazione.

### <a name="application-operator"></a>Application Operator (Operatore applicazioni)

Gli utenti con ruolo **Application Operator** (Operatore applicazioni) non hanno accesso alla pagina **Application Builder** (Generatore applicazioni). Non possono gestire l'applicazione.

## <a name="manage-users"></a>Gestire gli utenti

Gli amministratori dell'applicazione possono assegnare agli utenti i ruoli nell'applicazione.

### <a name="add-users"></a>Aggiungere utenti

Ogni utente deve avere un account utente prima di poter accedere a un'applicazione Azure IoT Central. Gli account Microsoft (account del servizio gestito) e gli account di Azure Active Directory (Azure AD) sono supportati in Azure IoT Central. I gruppi di Azure Active Directory non sono attualmente supportati in Azure IoT Central.

Per altre informazioni, vedere [Guida di Account Microsoft](https://support.microsoft.com/products/microsoft-account?category=manage-account) e [Guida introduttiva: Aggiungere nuovi utenti ad Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Per aggiungere un account utente a un'applicazione Azure IoT Central, usare il menu di spostamento secondario per accedere alla pagina **Utenti** della sezione **Amministrazione**.

    ![Elenco di utenti](media\howto-administer\image1.png)

1. Per aggiungere un utente, nella pagina **Utenti** scegliere **+ Aggiungi utente**.

    ![Add user](media\howto-administer\image2.png)

1. Scegliere un ruolo per l'utente dal menu a discesa **Ruolo**. Per altre informazioni sui ruoli, vedere la sezione *Ruoli in Azure IoT Central* nell'articolo.

    ![Selezione ruoli](media\howto-administer\image3.png)

    > [!NOTE]
    >  Per aggiungere utenti in blocco, immettere gli ID utente di tutti gli utenti che si vuole aggiungere, separati da punti e virgola. Scegliere un ruolo dal menu a discesa **Ruolo**. Selezionare quindi **Salva**.

1. Dopo aver aggiunto un utente, viene visualizzata una voce per l'utente nella pagina **Users** (Utenti).

    ![Elenco utenti](media\howto-administer\image4.png)

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Modificare i ruoli assegnati agli utenti

I ruoli non possono essere modificati dopo che sono stati assegnati. Per modificare il ruolo assegnato a un utente, eliminare l'utente e aggiungerlo nuovamente con un ruolo diverso.

### <a name="delete-users"></a>Eliminare gli utenti

Per eliminare gli utenti, selezionare una o più caselle di controllo nella pagina **Utenti**. Selezionare **Elimina**.

## <a name="view-your-bill"></a>Visualizzare la fattura

Per visualizzare la fattura, passare alla pagina **Fatturazione** nella sezione **Amministrazione** e selezionare **Fatturazione**. Verrà visualizzata una nuova scheda della pagina di fatturazione di Azure dove sarà possibile visualizzare le fatture per ogni applicazione Azure IoT Central.

## <a name="convert-your-trial-to-a-paid-application"></a>Convertire la versione di valutazione in un'applicazione a pagamento

Dopo avere valutato IoT Central, è possibile convertire la versione di valutazione nella versione a pagamento. Per completare il processo self-service, seguire questa procedura:

1. Usare il menu di spostamento secondario per passare alla pagina **Fatturazione** della sezione **Amministrazione**. Se la versione di valutazione non è stata estesa, la pagina è simile alla seguente:

    ![Stato della versione di valutazione gratuita](media/howto-administer/freetrial.png)

2. Selezionare **Convert to Paid** (Converti in app a pagamento). Se la versione di valutazione non è stata estesa, la finestra popup è simile alla seguente:

    ![Estendere la versione di valutazione gratuita](media/howto-administer/extend.png)

3. Nella finestra popup selezionare il tenant di Azure Active Directory appropriato e quindi la sottoscrizione di Azure da usare per l'applicazione IoT Central.

3. Dopo avere selezionato **Convert** (Converti), la versione di valutazione viene convertita in un'applicazione che da questo momento sarà a pagamento.

## <a name="extend-your-free-trial"></a>Estendere la versione di valutazione gratuita

Per impostazione predefinita, tutte le versioni di valutazione gratuite sono disponibili per sette giorni. Se si vuole estendere la versione di valutazione gratuita a 30 giorni, seguire questa procedura:

1. Usare il menu di spostamento secondario per passare alla pagina **Fatturazione** della sezione **Amministrazione**.

1. Selezionare **Estendi la versione di valutazione**. Nella finestra popup selezionare il tenant di Azure Active Directory appropriato e quindi la sottoscrizione di Azure da usare per l'applicazione IoT Central.

1. Selezionare quindi **Estendi**. La versione di valutazione ora è valida per 30 giorni.

## <a name="use-the-azure-sdks-for-control-plane-operations"></a>Usare gli Azure SDK per le operazioni del piano di controllo

I pacchetti SDK di Azure Resource Manager di IoT Central sono disponibili per Node, Python, C#, Ruby, Java e Go. Queste librerie supportano le operazioni del piano di controllo per IoT Central, che consentono di creare, elencare, aggiornare o eliminare le applicazioni IoT Central. Offrono inoltre gli helper per supportare l'autenticazione e per la gestione degli errori che è specifica per ogni lingua. 

È possibile trovare esempi di come usare gli SDK di Azure Resource Manager in [https://github.com/emgarten/iotcentral-arm-sdk-examples](https://github.com/emgarten/iotcentral-arm-sdk-examples).

Per altre informazioni, dare un'occhiata a questi pacchetti in GitHub.

| Linguaggio | Repository | Pacchetto |
| ---------| ---------- | ------- |
| Nodo | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Go | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>Passaggi successivi

Dopo avere appreso come gestire l'applicazione Azure IoT Central, il prossimo passaggio suggerito è:

> [!div class="nextstepaction"]
> [Configurare il modello del dispositivo](howto-set-up-template.md)
