---
title: Gestire gruppi di app per Desktop virtuale Windows con il portale - Azure
description: Come gestire i gruppi di app di Desktop virtuale Windows con il portale di Azure.
author: Heidilohr
ms.topic: tutorial
ms.date: 10/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ec26f021ffa581b0713973904c97349df83a08ef
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91930272"
---
# <a name="tutorial-manage-app-groups-with-the-azure-portal"></a>Esercitazione: Gestire gruppi di app con il portale di Azure

>[!IMPORTANT]
>Questo contenuto si applica a Desktop virtuale Windows con gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si usa Desktop virtuale Windows (versione classica) senza gli oggetti Azure Resource Manager, vedere [questo articolo](./virtual-desktop-fall-2019/manage-app-groups-2019.md).

Il gruppo di app predefinito creato per un nuovo pool di host di Desktop virtuale Windows pubblica anche il desktop completo. È inoltre possibile creare uno o più gruppi di applicazioni RemoteApp per il pool di host. Seguire questa esercitazione per creare un gruppo di app RemoteApp e pubblicare singole app nel menu Start.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un gruppo di app RemoteApp.
> * Concedere l'accesso ai programmi RemoteApp.

## <a name="create-a-remoteapp-group"></a>Creare un gruppo di app RemoteApp

Se è già stato creato un pool di host e le macchine virtuali dell'host di sessione usando il portale di Azure o PowerShell, è possibile aggiungere gruppi di applicazioni dal portale di Azure con il processo seguente:

1.  Accedere al [portale di Azure](https://portal.azure.com/).
   
    >[!NOTE]
    > Se si accede al portale US Gov, passare invece a [https://portal.azure.us/](https://portal.azure.us/).

2.  Cercare e selezionare **Desktop virtuale Windows**.

3. È possibile aggiungere un gruppo di applicazioni direttamente oppure da un pool host esistente. Scegliere una delle opzioni seguenti:

    - Selezionare **Gruppi di applicazioni** nel menu sul lato sinistro della pagina e quindi selezionare **+ Aggiungi**.

    - Selezionare **Pool di host** nel menu sul lato sinistro della schermata, selezionare il nome del pool di host. Selezionare quindi **Gruppi di applicazioni** nel menu sul lato sinistro e infine selezionare **+ Aggiungi**. In questo caso il pool di host risulterà già selezionato nella scheda Informazioni di base.

4. Nella scheda **Informazioni di base** selezionare la **sottoscrizione** e il **gruppo di risorse** per cui si intende creare il gruppo di app. È anche possibile scegliere di creare un nuovo gruppo di risorse anziché selezionarne uno esistente.

5. Nel menu a discesa selezionare il **pool di host** che verrà associato al gruppo di applicazioni.

    >[!NOTE]
    >È necessario selezionare il pool di host associato al gruppo di applicazioni. I gruppi di app hanno app o desktop gestiti da un host di sessione e gli host di sessione fanno parte dei pool di host. Il gruppo di app deve essere associato a un pool di host durante la creazione.

    > [!div class="mx-imgBorder"]
    > ![Screenshot della scheda Informazioni di base nel portale di Azure.](media/basics-tab.png)

6. Selezionare **RemoteApp** in **Tipo di gruppo di applicazioni** e quindi immettere un nome per RemoteApp.

      > [!div class="mx-imgBorder"]
      > ![Screenshot dei campi di Tipo di gruppo di applicazioni. "RemoteApp" è evidenziato.](media/remoteapp-button.png)

7.  Selezionare **Avanti: Assegnazioni >** .

8.  Per assegnare singoli utenti o gruppi di utenti nel gruppo di app, selezionare **+ Aggiungi utenti o gruppi di utenti di Azure AD**.

9.  Selezionare gli utenti a quali consentire l'accesso alle app. È possibile selezionare uno o più utenti e gruppi di utenti.

     > [!div class="mx-imgBorder"]
     > ![Screenshot del menu di selezione dell'utente.](media/select-users.png)

10.  Scegliere **Seleziona**.

11.  Selezionare **Avanti: Applicazioni >** e quindi **+Aggiungi applicazioni**.

12.  Per aggiungere un'applicazione dal menu Start:

      - In **Origine applicazione** selezionare **Menu Start** dal menu a discesa. In **Applicazione** scegliere quindi l'applicazione dal menu a discesa.

     > [!div class="mx-imgBorder"]
     > ![Screenshot della schermata Aggiungi applicazione con il menu Start selezionato.](media/add-app-start.png)

      - In **Nome visualizzato** immettere il nome dell'applicazione che verrà visualizzato dall'utente nel client.

      - Lasciare invariate le altre opzioni e selezionare **Salva**.

13.  Per aggiungere un'applicazione da un percorso di file specifico:

      - In **Origine applicazione** selezionare **Percorso file** nel menu a discesa.

      - In **Percorso applicazione** immettere il percorso dell'applicazione nell'host di sessione registrato con il pool di host associato.

      - Immettere i dettagli dell'applicazione nei campi **Nome applicazione**, **Nome visualizzato**, **Percorso icone** e **Indice icone**.

      - Selezionare **Salva**.

     > [!div class="mx-imgBorder"]
     > ![Screenshot della pagina Aggiungi applicazione con il percorso file selezionato.](media/add-app-file.png)

14.  Ripetere questo processo per tutte le applicazioni che si vuole aggiungere al gruppo di applicazioni.

15.  Selezionare quindi **Avanti: Area di lavoro >** .

16.  Se si vuole registrare il gruppo di app in un'area di lavoro, selezionare **Sì** per **Registra gruppo di applicazioni**. Se si preferisce registrare il gruppo di app in un secondo momento, selezionare **No**.

17.  Se si seleziona **Sì**, è possibile selezionare un'area di lavoro esistente in cui registrare il gruppo di app.

       >[!NOTE]
       >È possibile registrare il gruppo di app solo nelle aree di lavoro create nella stessa posizione del pool di host. Inoltre, se in precedenza è stato registrato un altro gruppo di app dallo stesso pool di host del nuovo gruppo di app in un'area di lavoro, questo verrà selezionato e non sarà possibile modificarlo. Tutti i gruppi di app di un pool di host devono essere registrati nella stessa area di lavoro.

     > [!div class="mx-imgBorder"]
     > ![Screenshot della pagina Registra gruppo di applicazioni per un'area di lavoro già esistente. Il pool di host è preselezionato.](media/register-existing.png)

18.  Facoltativamente, se si vogliono creare tag per semplificare l'organizzazione dell'area di lavoro, selezionare **Avanti: Tag >** e immettere i nomi dei tag.

19.  Al termine, selezionare **Rivedi e crea**.

20.  Attendere il completamento del processo di convalida. Al termine, selezionare **Crea** per distribuire il gruppo di app.

Il processo di distribuzione eseguirà le operazioni seguenti:

- Creare il gruppo di app RemoteApp.
- Aggiungere le app selezionate al gruppo di app.
- Pubblicare il gruppo di app pubblicato per gli utenti e i gruppi di utenti selezionati.
- Registrare il gruppo di app, se si è scelto di eseguire questa operazione.
- Creare un collegamento a un modello di Azure Resource Manager in base alla configurazione che è possibile scaricare e salvare per un uso successivo.

>[!IMPORTANT]
>È possibile creare solo 200 gruppi di applicazioni per ogni tenant di Azure Active Directory. Questo limite è stato aggiunto a causa di limitazioni del servizio per il recupero di feed per gli utenti. Il limite non si applica ai gruppi di app creati in Desktop virtuale Windows (classico).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come creare un gruppo di app, popolarlo con programmi RemoteApp e assegnare utenti. Per informazioni su come creare un pool di host di convalida, vedere l'esercitazione seguente. È possibile usare un pool di host di convalida per monitorare gli aggiornamenti del servizio prima di distribuirli nell'ambiente di produzione.

> [!div class="nextstepaction"]
> [Creare un pool di host per convalidare gli aggiornamenti dei servizi](./create-validation-host-pool.md)
