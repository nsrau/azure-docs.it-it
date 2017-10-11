---
title: Controllo degli accessi in base al ruolo per controllare i diritti di accesso e gestire le richieste di supporto | Documentazione Microsoft
description: Controllo degli accessi in base al ruolo di Azure per controllare i diritti di accesso e gestire le richieste di supporto
author: ganganarayanan
ms.author: gangan
ms.date: 1/31/2017
ms.topic: article
ms.service: microsoft-docs
ms.assetid: 58a0ca9d-86d2-469a-9714-3b8320c33cf5
ms.openlocfilehash: 20ebd324cbf379980b43d255d468673de2b6d950
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="azure-role-based-access-control-rbac-to-control-access-rights-to-create-and-manage-support-requests"></a>Controllo degli accessi in base al ruolo di Azure per controllare i diritti di accesso e gestire le richieste di supporto

Il [Controllo degli accessi in base al ruolo di Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is) consente una gestione specifica degli accessi per Azure.
La creazione di richieste di supporto nel portale di Azure, [portal.azure.com](https://portal.azure.com), usa il modello di Controllo degli accessi in base al ruolo per definire chi può creare e gestire le richieste di supporto.
L'accesso viene concesso assegnando ruoli di Controllo degli accessi in base al ruolo appropriati a utenti, gruppi e applicazioni in un ambito specifico: una sottoscrizione, un gruppo di risorse o una risorsa.

Esaminiamo l'esempio di un proprietario di un gruppo di risorse con autorizzazioni di lettura nell'ambito della sottoscrizione che può gestire tutte le risorse nel gruppo di risorse, come siti Web, macchine virtuali e subnet.
Quando tuttavia tenta di creare una richiesta di supporto per la risorsa macchina virtuale, si verifica l'errore seguente

![Errore relativo alla sottoscrizione](./media/create-manage-support-requests-using-access-control/subscription-error.png)

Per la gestione delle richieste di supporto è necessaria l'autorizzazione di scrittura o un ruolo con l'azione Microsoft.Support/* nell'ambito della sottoscrizione in grado di creare e gestire le richieste di supporto.

L'articolo seguente illustra come è possibile usare il Controllo degli accessi in base al ruolo di Azure per creare e gestire le richieste di supporto nel portale di Azure.

## <a name="getting-started"></a>Introduzione

Nell'esempio precedente è possibile creare una richiesta di supporto per la risorsa se il proprietario della sottoscrizione ha assegnato un ruolo personalizzato di Controllo degli accessi in base al ruolo nella sottoscrizione.
I [ruoli personalizzati di Controllo degli accessi in base al ruolo](https://azure.microsoft.com/documentation/articles/role-based-access-control-custom-roles/) possono essere creati usando Azure PowerShell, l'interfaccia della riga di comando di Azure e l'API REST.

La proprietà Actions di un ruolo personalizzato specifica le operazioni di Azure a cui il ruolo concede l'accesso.
Per creare un ruolo personalizzato per la gestione delle richieste di supporto, il ruolo deve includere l'azione Microsoft.Support/*

Di seguito è riportato un esempio di un ruolo personalizzato che consente di creare e gestire le richieste di supporto.
Questo ruolo è stato denominato "Support Request Contributor" ed ecco come si fa riferimento al ruolo personalizzato in questo articolo.

``` Json
{
    "Name":  "Support Request Contributor",
    "Id":  "1f2aad59-39b0-41da-b052-2fb070bd7942",
    "IsCustom":  true,
    "Description":  "Lets you create and manage support tickets.",
    "Actions":  [
                    "Microsoft.Support/*"
                ],
    "NotActions":  [
                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

Seguire i passaggi descritti in [questo video](https://www.youtube.com/watch?v=-PaBaDmfwKI) per imparare a creare un ruolo personalizzato per la sottoscrizione.

## <a name="create-and-manage-support-requests-in-the-azure-portal"></a>Creare e gestire le richieste di supporto nel portale di Azure

Esaminiamo un esempio in cui si è proprietari della sottoscrizione "Visual Studio MSDN Subscription".
Joe è il collega proprietario delle risorse in alcuni gruppi di risorse nella sottoscrizione e autorizzazione di lettura per la sottoscrizione.
Si vuole concedere al collega Joe l'accesso per poter creare e gestire i ticket di supporto per le risorse in questa sottoscrizione.

1. Il primo passaggio consiste nel passare alla sottoscrizione e in "Impostazioni" viene visualizzato un elenco di utenti. Fare clic sull'utente Joe che ha accesso in lettura per la sottoscrizione e assegnargli un nuovo ruolo personalizzato.

    ![Aggiungi ruolo](./media/create-manage-support-requests-using-access-control/add-role.png)

2. Fare clic su "Aggiungi" nel pannello "Utenti". Selezionare il ruolo personalizzato di "Support Request Contributor" nell'elenco di ruoli

    ![Aggiungere il ruolo di collaboratore di supporto](./media/create-manage-support-requests-using-access-control/add-support-contributor-role.png)

3. Dopo avere selezionato il nome del ruolo, fare clic su "Aggiungi utenti" e immettere le credenziali di posta elettronica di Joe. Fare clic su "Seleziona"

    ![Aggiungi utenti](./media/create-manage-support-requests-using-access-control/add-users.png)

4. Fare clic su "OK" per procedere

    ![Aggiungere un accesso](./media/create-manage-support-requests-using-access-control/add-access.png)

5. Si noti ora che l'utente con il ruolo personalizzato appena aggiunto "Support Request Contributor" diventa visibile nella sottoscrizione di cui si è proprietari

    ![Utente aggiunto](./media/create-manage-support-requests-using-access-control/user-added.png)

    Quando Joe accederà al portale, la sottoscrizione a cui è stato aggiunto risulterà visibile.

7. Joe fa clic su "Nuova richiesta di supporto" nel pannello "Guida e supporto tecnico" ed è in grado di creare richieste di supporto per "Visual Studio Ultimate con MSDN"

    ![Nuova richiesta di supporto](./media/create-manage-support-requests-using-access-control/new-support-request.png)

8. Fare clic su "Tutte le richieste di supporto" Joe possibile visualizzare l'elenco delle richieste di supporto creato per questa sottoscrizione ![Case visualizzazione dettagli](./media/create-manage-support-requests-using-access-control/case-details-view.png)

## <a name="remove-support-request-access-in-the-azure-portal"></a>Rimuovere l'accesso alle richieste di supporto nel portale di Azure

Esattamente come è possibile concedere l'accesso a un utente per creare e gestire le richieste di supporto, è possibile anche rimuovere l'accesso per l'utente.
Per rimuovere la possibilità di creare e gestire le richieste di supporto, passare alla sottoscrizione, fare clic su "Impostazioni", quindi sull'utente, in questo caso, Joe.
Fare clic con il pulsante destro del mouse sul nome del ruolo "Support Request Contributor" e fare clic su "Rimuovi"

![Rimuovere l'accesso alle richiesta di supporto](./media/create-manage-support-requests-using-access-control/remove-support-request-access.png)

Quando Joe eseguirà l'accesso al portale e tenterà di creare una richiesta di supporto, verrà visualizzato l'errore seguente

![Errore di sottoscrizione 2](./media/create-manage-support-requests-using-access-control/subscription-error-2.png)

Quando Joe fa clic su "Tutte le richieste di supporto", non sarà visibile nessuna richiesta di supporto

![Visualizzazione dettagli del caso 2](./media/create-manage-support-requests-using-access-control/case-details-view-2.png)
