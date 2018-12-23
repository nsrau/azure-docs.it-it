---
title: Gestire un'applicazione Azure IoT Central | Microsoft Docs
description: Informazioni sulla gestione di applicazioni Azure IoT Central destinate agli amministratori
author: viv-liu
ms.author: viviali
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 4dba6aff2002c1f17055cd647be94c4d8f644ad7
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2018
ms.locfileid: "52992213"
---
# <a name="administer-your-iot-central-application"></a>Amministrare l'applicazione IoT Central

Dopo aver creato un'applicazione IoT Central, è possibile passare alla sezione **Amministrazione** per:

- Gestire le impostazioni dell'applicazione
- Gestire gli utenti
- Gestire i ruoli
- Visualizzare la fattura
- Convertire la versione di valutazione in applicazione con pagamento in base al consumo
- Esportazione dei dati
- Gestire la connessione del dispositivo
- Usare token di accesso

Per accedere alla sezione **Amministrazione** e usarla, è necessario disporre del ruolo **Amministratore** per l'applicazione Azure IoT Central. All'utente che crea un'applicazione Azure IoT Central viene automaticamente assegnato il ruolo **Amministratore** per l'applicazione. La sezione [Gestire gli utenti](#manage-users) in questo articolo include altre informazioni su come assegnare il ruolo **Administrator** ad altri utenti.

## <a name="manage-application-settings"></a>Gestire le impostazioni dell'applicazione

### <a name="change-application-name-and-url"></a>Modificare il nome e l'URL dell'applicazione
Nella pagina **Impostazioni applicazione** è possibile modificare il nome e l'URL dell'applicazione e quindi selezionare **Salva**.

![Pagina delle impostazioni dell'applicazione](media/howto-administer/image0-a.png)

> [!Note]
> Se si modifica l'URL, l'URL precedente può essere usato da un altro cliente di Azure IoT Central. In tal caso non sarà più disponibile. L'URL precedente a questo punto non funziona più ed è necessario indicare agli utenti quello nuovo da usare.

### <a name="prepare-and-upload-image"></a>Preparare e caricare immagini
Per modificare l'immagine dell'applicazione, vedere [Preparare e caricare immagini nell'applicazione Azure IoT Central](howto-prepare-images.md).

### <a name="copy-an-application"></a>Copiare un'applicazione
È possibile creare una copia di una qualsiasi applicazione, senza tuttavia includere eventuali istanze di dispositivi, la cronologia dei dati dei dispositivi e i dati degli utenti. La copia sarà un'applicazione con pagamento in base al consumo alla quale verrà applicato un addebito. Non è possibile creare una versione di valutazione di un'applicazione in questo modo.

Fare clic sul pulsante **Copia**. Nella finestra di dialogo immettere i dettagli per la nuova applicazione con pagamento in base al consumo. Fare clic sul pulsante **Copia** per confermare che si vuole procedere. Altre informazioni sui campi in questo modulo sono disponibili nella guida introduttiva [Creare un'applicazione](quick-deploy-iot-central.md).

![Pagina delle impostazioni dell'applicazione](media/howto-administer/appCopy2.png)

Al termine dell'operazione di copia dell'app, è possibile passare alla nuova applicazione copiata tramite il collegamento visualizzato.

![Pagina delle impostazioni dell'applicazione](media/howto-administer/appCopy3.png)

> [!Note]
> La copia di un'applicazione comporta anche la copia della definizione di regole e azioni. Tuttavia, poiché gli utenti che hanno accesso all'applicazione originale non vengono copiati nella nuova app, è necessario aggiungere manualmente utenti ad azioni come la posta elettronica per cui gli utenti sono un prerequisito. In generale è consigliabile verificare le regole e le azioni per assicurarsi che siano aggiornate nella nuova app.

### <a name="delete-an-application"></a>Eliminare un'applicazione

> [!Note]
> Per eliminare un'applicazione, è necessario disporre anche delle autorizzazioni per eliminare risorse nella sottoscrizione di Azure scelta al momento della creazione dell'applicazione. Per altre informazioni, vedere [Usare il controllo degli accessi in base al ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

Usare il pulsante **Elimina** per eliminare definitivamente l'applicazione IoT Central. In questo modo, verranno eliminati in modo permanente tutti i dati associati all'applicazione.

## <a name="manage-users"></a>Gestire gli utenti

### <a name="add-users"></a>Aggiungere utenti

Ogni utente deve avere un account utente prima di poter accedere a un'applicazione Azure IoT Central. Gli account Microsoft (account del servizio gestito) e gli account di Azure Active Directory (Azure AD) sono supportati in Azure IoT Central. I gruppi di Azure Active Directory non sono attualmente supportati in Azure IoT Central.

Per altre informazioni, vedere [Guida account Microsoft](https://support.microsoft.com/products/microsoft-account?category=manage-account) e [Guida introduttiva: aggiungere nuovi utenti ad Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Per aggiungere un utente a un'applicazione IoT Central, passare alla pagina **Utenti** nella sezione **Amministrazione**.

    ![Elenco di utenti](media/howto-administer/image1.png)

1. Per aggiungere un utente, nella pagina **Utenti** scegliere **+ Aggiungi utente**.

1. Scegliere un ruolo per l'utente dal menu a discesa **Ruolo**. Per altre informazioni sui ruoli, vedere la sezione [Gestire i ruoli](#manage-roles) in questo articolo.

    ![Selezione ruoli](media/howto-administer/image3.png)

    > [!NOTE]
    >  Per aggiungere utenti in blocco, immettere gli ID utente di tutti gli utenti che si vuole aggiungere, separati da punti e virgola. Scegliere un ruolo dal menu a discesa **Ruolo**. Selezionare quindi **Salva**.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Modificare i ruoli assegnati agli utenti

I ruoli non possono essere modificati dopo che sono stati assegnati. Per modificare il ruolo assegnato a un utente, eliminare l'utente e aggiungerlo nuovamente con un ruolo diverso.

### <a name="delete-users"></a>Eliminare gli utenti

Per eliminare gli utenti, selezionare una o più caselle di controllo nella pagina **Utenti**. Selezionare **Elimina**.

## <a name="manage-roles"></a>Gestire i ruoli

I ruoli permettono di controllare quali utenti all'interno dell'organizzazione possono eseguire varie attività in IoT Central. Agli utenti dell'applicazione è possibile assegnare tre ruoli diversi. 

### <a name="administrator"></a>Amministratore

Gli utenti con ruolo **Administrator** hanno accesso a tutte le funzionalità in un'applicazione.

All'utente che ha creato un'applicazione viene assegnato automaticamente il ruolo **Amministratore**. Ci deve sempre essere almeno un utente con il ruolo **Administrator** (Amministratore).

### <a name="application-builder"></a>Application Builder (Generatore applicazioni)

Gli utenti con ruolo **Application Builder** (Compilatore applicazioni) possono eseguire tutte le operazioni in un'applicazione ad eccezione dell'amministrazione dell'applicazione. Di conseguenza, possono creare, modificare ed eliminare modelli di dispositivo e dispositivi, gestire set di dispositivi ed eseguire analisi e processi. Questi utenti non hanno accesso alla sezione **Amministrazione** dell'applicazione.

### <a name="application-operator"></a>Application Operator (Operatore applicazioni)

Gli utenti con ruolo **Application Operator** (Operatore applicazioni) non possono apportare modifiche ai modelli di dispositivo né amministrare l'applicazione. Di conseguenza, possono aggiungere e modificare dispositivi, gestire set di dispositivi ed eseguire analisi e processi. Questi utenti non hanno accesso alle pagine **Application Builder** (Compilatore applicazioni) e **Amministrazione** dell'applicazione.


## <a name="view-your-bill"></a>Visualizzare la fattura

Per visualizzare la fattura, passare alla pagina **Fatturazione** nella sezione **Amministrazione** Verrà visualizzata una nuova scheda della pagina di fatturazione di Azure dove sarà possibile visualizzare le fatture per ogni applicazione Azure IoT Central.

### <a name="convert-your-trial-to-pay-as-you-go"></a>Convertire la versione di valutazione in applicazione con pagamento in base al consumo

È possibile convertire la versione di valutazione dell'applicazione in applicazione con pagamento in base al consumo. Ecco le differenze tra questi tipi di applicazioni.

- Le applicazioni di tipo **Versione di valutazione** sono gratuite per 7 giorni prima della scadenza. Possono essere convertite in applicazioni con pagamento in base al consumo in qualsiasi momento prima della scadenza.
- Le applicazioni di tipo **Pagamento in base al consumo** prevedono un addebito per ogni dispositivo, con i primi 5 dispositivi offerti gratuitamente.

Per altre informazioni sui prezzi, vedere la [pagina dei prezzi di Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).
    
Per completare il processo self-service, seguire questa procedura:

1. Passare alla pagina **Fatturazione** nella sezione **Amministrazione**. 

    ![Stato della versione di valutazione](media/howto-administer/freetrialbilling.png)

1. Fare clic su **Convert to Pay-As-You-Go** (Converti in applicazione con pagamento in base al consumo). 

    ![Convertire la versione di valutazione](media/howto-administer/convert.png)

1. Selezionare l'istanza di Azure Active Directory appropriata e quindi la sottoscrizione di Azure da usare per l'applicazione con pagamento in base al consumo.

1. Dopo aver fatto clic su **Converti**, l'applicazione diventa una versione con pagamento in base al consumo e viene avviata la fatturazione.

## <a name="export-data"></a>Esportazione dei dati

È possibile abilitare la funzionalità **Esportazione continua dei dati** per esportare dati di misurazioni, dispositivi e modelli di dispositivo nell'account di archiviazione BLOB di Azure. Per altre informazioni, vedere [Come esportare i dati](#howto-export-data).

## <a name="manage-device-connection"></a>Gestire la connessione del dispositivo

Connettere i dispositivi su larga scala nell'applicazione usando le chiavi e i certificati indicati qui. Per altre informazioni, vedere [Connessione dei dispositivi](#concepts-connectivity).

## <a name="use-access-tokens"></a>Usare token di accesso

Generare token di accesso da usare negli strumenti di sviluppo. Attualmente è disponibile un solo strumento di sviluppo, lo strumento di esplorazione di IoT Central per il monitoraggio dei messaggi dei dispositivo e delle modifiche apportate a proprietà e impostazioni. Per altre informazioni, vedere [IoT Central explorer](#howto-use-iotc-explorer) (Strumento di esplorazione di IoT Central). 

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
