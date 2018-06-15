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
ms.openlocfilehash: 0bdd798f9bc8fdaef54abd721a986c607c6323a5
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628556"
---
# <a name="how-to-administer-your-application"></a>Come gestire l'applicazione

Dopo aver creato un'applicazione Microsoft Azure IoT Central, è possibile usare la sezione **Administration** (Gestione) dell'interfaccia utente di Azure IoT Central per gestirla. Per passare alla sezione **Administration** (Gestione), scegliere **Administration** (Gestione) nel menu di spostamento a sinistra.

La sezione **Administration** (Gestione) consente di:

- Gestire gli utenti

- Gestire i ruoli

- Visualizzare le informazioni di fatturazione

- Gestire le impostazioni dell'applicazione

- Estendere una versione di valutazione gratuita

Nella sezione **Administration** (Gestione) è presente un menu di spostamento secondario con collegamenti alle varie attività di gestione.

Per accedere alla sezione **Administration** (Gestione) e usarla, è necessario avere il ruolo **Administrator** (Amministratore) nell'applicazione Azure IoT Central. All'utente che crea un'applicazione Azure IoT Central viene automaticamente assegnato il ruolo **Administrator** (Amministratore) dell'applicazione. La sezione *Gestire gli utenti* più avanti nell'articolo include altre informazioni su come assegnare il ruolo **Administrator** (Amministratore) ad altri utenti.

## <a name="change-application-name"></a>Modificare il nome dell'applicazione

Per modificare il nome dell'applicazione, usare il menu di spostamento secondario per passare alla pagina **Application Settings** (Impostazioni applicazione) nella sezione **Administration** (Amministrazione).

Nella pagina **Application Settings** (Impostazioni applicazione) immettere un nome di propria scelta nel campo **Application Name** (Nome applicazione) e scegliere **Save** (Salva).

## <a name="change-the-application-url"></a>Modificare l'URL dell'applicazione

Per modificare l'URL dell'applicazione, usare il menu di spostamento secondario per passare alla pagina **Application Settings** (Impostazioni applicazione) nella sezione **Administration** (Amministrazione).

![Pagina Application Settings (Impostazioni applicazione)](media\howto-administer\image0-a.png)

Nella pagina **Application Settings** (Impostazioni applicazione) immettere l'URL di propria scelta nel campo **URL** e scegliere **Save** (Salva). L'URL deve avere una lunghezza massima di 200 caratteri. Se l'URL non è disponibile, viene visualizzato un errore di convalida

> [!Note]
> Se si modifica l'URL, l'URL precedente può essere usato da un altro cliente di Azure IoT Central. In tal caso, non sarà più disponibile. Quando si modifica l'URL, l'URL precedente non funziona più ed è necessario informare gli utenti in merito al nuovo URL da usare.

## <a name="change-the-application-image"></a>Modificare l'immagine dell'applicazione

Per altre informazioni sull'uso di immagini in un'applicazione Azure IoT Central, vedere [Prepare and upload images to your Azure IoT Central application](howto-prepare-images.md) (Preparare e caricare immagini nell'applicazione Azure IoT Central).

## <a name="delete-an-application"></a>Eliminare un'applicazione

Per modificare l'applicazione, usare il menu di spostamento secondario per passare alla pagina **Application Settings** (Impostazioni applicazione) nella sezione **Administration** (Amministrazione).

Scegliere **Elimina**.

> [!Note]
> Quando si elimina un'applicazione tutti i dati associati a tale applicazione vengono eliminati in modo permanente. Per eliminare un'applicazione, è necessario anche avere i diritti per eliminare le risorse nella sottoscrizione di Azure scelta al momento della creazione dell'applicazione. Per altre informazioni, vedere [Usare il controllo degli accessi in base al ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

## <a name="roles-in-azure-iot-central"></a>Ruoli in Azure IoT Central

I ruoli consentono di controllare quali utenti, all'interno dell'organizzazione, possono eseguire varie attività in Azure IoT Central. In Azure IoT Central sono disponibili tre ruoli che è possibile assegnare agli utenti dell'applicazione. I ruoli vengono assegnati da ogni applicazione. Lo stesso utente può avere ruoli diversi in applicazioni diverse. È possibile assegnare allo stesso utente più ruoli all'interno di un'applicazione.

### <a name="administrator"></a>Amministratore

Gli utenti con ruolo **Administrator** (Amministratore) hanno accesso a tutte le funzionalità in un'applicazione Azure IoT Central.

All'utente che ha creato un'applicazione viene assegnato automaticamente il ruolo **Administrator** (Amministratore). Ci deve sempre essere almeno un utente con il ruolo **Administrator** (Amministratore).

### <a name="application-builder"></a>Application Builder (Generatore applicazioni)

Gli utenti che hanno il ruolo **Application Builder** possono eseguire tutte le operazioni in un'applicazione Azure IoT Central ad eccezione della gestione dell'applicazione.

### <a name="application-operator"></a>Application Operator (Operatore applicazioni)

Gli utenti con ruolo **Application Operator** (Operatore applicazioni) non hanno accesso alla pagina **Application Builder** (Generatore applicazioni). Non possono gestire l'applicazione.

## <a name="manage-users"></a>Gestire gli utenti

Gli amministratori dell'applicazione possono assegnare agli utenti i ruoli nell'applicazione.

### <a name="add-users"></a>Aggiungere utenti

Ogni utente deve avere un account utente prima di poter accedere a un'applicazione Azure IoT Central. Gli account Microsoft e gli account di Azure Active Directory (AAD) sono supportati in Azure IoT Central. I gruppi di Azure Active Directory non sono attualmente supportati in Azure IoT Central.

Per altre informazioni, vedere [Guida di Account Microsoft](https://support.microsoft.com/products/microsoft-account?category=manage-account) e [Guida introduttiva: Aggiungere nuovi utenti ad anteprima di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Per aggiungere un account utente all'applicazione Azure IoT Central, usare il menu di spostamento secondario per passare alla pagina **Users** (Utenti) della sezione **Administration** (Amministrazione).

    ![Elenco di utenti](media\howto-administer\image1.png)

1. Nella pagina **Users** (Utenti) scegliere **+ Add user** (+ Aggiungi utente) per aggiungere un utente.

    ![Aggiunta di un utente](media\howto-administer\image2.png)

1. Quando si aggiunge un utente all'applicazione Azure IoT Central, scegliere un ruolo per l'utente dall'elenco a discesa **Role** (Ruolo). Per altre informazioni sui ruoli, vedere la sezione *Ruoli in Azure IoT Central* nell'articolo.

    ![Selezione ruoli](media\howto-administer\image3.png)

    > [!NOTE]
    >  Per aggiungere utenti in blocco, immettere gli ID utente di tutti gli utenti che si vuole aggiungere separati da punti e virgola. Scegliere un ruolo dall'elenco a discesa **Role** (Ruolo) e scegliere **Save** (Salva).

1. Dopo aver aggiunto un utente, viene visualizzata una voce per l'utente nella pagina **Users** (Utenti).

    ![Elenco utenti](media\howto-administer\image4.png)

### <a name="edit-the-roles-assigned-to-users"></a>Modificare i ruoli assegnati agli utenti

Una volta assegnati, i ruoli non possono essere modificati. Per modificare il ruolo assegnato a un utente, eliminare l'utente e aggiungerlo nuovamente con un ruolo diverso.

### <a name="delete-users"></a>Eliminare gli utenti

Per eliminare gli utenti, selezionare una o più caselle di controllo nella pagina **Users** (Utenti) e scegliere **Delete** (Elimina).

## <a name="view-your-bill"></a>Visualizzare la fattura

Per visualizzare la fattura, passare alla pagina **Billing** (Fatturazione) nella sezione **Administration** (Gestione) e scegliere **Billing** (Fatturazione). La pagina della fatturazione viene aperta in una nuova scheda da dove è possibile visualizzare le fatture per ogni applicazione Azure IoT Central.

## <a name="convert-your-trial-to-a-paid-application"></a>Convertire la versione di valutazione in un'applicazione a pagamento

Dopo aver valutato IoT Central, è possibile convertire la versione di valutazione in un'applicazione a pagamento. Per completare il processo self-service, seguire questa procedura:

1. Usare il menu di spostamento secondario per passare alla pagina **Billing** (Fatturazione) della sezione **Administration** (Gestione). Se la versione di valutazione non è stata estesa, la pagina è simile alla seguente:

    ![Stato della versione di valutazione gratuita](media/howto-administer/freetrial.png)

2. Fare clic su **Convert to Paid** (Converti in app a pagamento). Se la versione di valutazione non è stata estesa, la finestra popup è simile alla seguente:

    Nella finestra popup selezionare il tenant di Azure Active Directory appropriato e la sottoscrizione di Azure che si vuole usare per l'applicazione IoT Central.

    ![Estendere la versione di valutazione gratuita](media/howto-administer/extend.png)

3. Dopo aver fatto clic su **Convert** (Converti), la versione di valutazione viene convertita in un'applicazione che da questo momento sarà a pagamento.

## <a name="extend-your-free-trial"></a>Estendere la versione di valutazione gratuita

Per impostazione predefinita, tutte le versioni di valutazione gratuita sono disponibili per 7 giorni. Se si vuole aumentare la valutazione gratuita fino a 30 giorni, seguire la procedura qui riportata:

1. Usare il menu di spostamento secondario per passare alla pagina **Billing** (Fatturazione) della sezione **Administration** (Gestione):

1. Fare clic su **Extend Trial** (Estendi versione di valutazione). Nella finestra popup selezionare il tenant di Azure Active Directory appropriato e la sottoscrizione di Azure da usare per l'applicazione IoT Central:

1. Fare clic su **Extend** (Estendi). La versione di valutazione ora è valida per 30 giorni.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come gestire l'applicazione Azure IoT Central, il prossimo passo suggerito è:

> [!div class="nextstepaction"]
> [Configurare il modello del dispositivo](howto-set-up-template.md)