---
title: Gestire gruppi di app per Desktop virtuale Windows con il portale - Azure
description: Come gestire i gruppi di app di Desktop virtuale Windows con il portale di Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f072ed8a758173645c886cabf0b20f9e123cbbab
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612158"
---
# <a name="tutorial-manage-app-groups-with-the-azure-portal"></a>Esercitazione: Gestire gruppi di app con il portale di Azure

>[!IMPORTANT]
>Questo contenuto si applica all'aggiornamento di Primavera 2020 con gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si usa la versione Autunno 2019 di Desktop virtuale Windows senza gli oggetti Azure Resource Manager, vedere [questo articolo](./virtual-desktop-fall-2019/manage-app-groups-2019.md).
>
> L'aggiornamento di Primavera 2020 di Desktop virtuale Windows è attualmente disponibile in anteprima pubblica. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. 
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Il gruppo di app predefinito creato per un nuovo pool di host di Desktop virtuale Windows pubblica anche il desktop completo. È inoltre possibile creare uno o più gruppi di applicazioni RemoteApp per il pool di host. Seguire questa esercitazione per creare un gruppo di app RemoteApp e pubblicare singole app nel menu Start.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un gruppo di app RemoteApp.
> * Concedere l'accesso ai programmi RemoteApp.

## <a name="create-a-remoteapp-group"></a>Creare un gruppo di app RemoteApp

Se è già stato creato un pool di host e le macchine virtuali dell'host di sessione usando il portale di Azure o PowerShell, è possibile aggiungere gruppi di applicazioni dal portale di Azure con il processo seguente:

1.  Accedere al [portale di Azure](https://portal.azure.com/).

2.  Cercare e selezionare **Desktop virtuale Windows**.

3.  Selezionare **Gruppi di applicazioni** nel menu sul lato sinistro della pagina, quindi selezionare **+ Aggiungi**.

4. Nella scheda **Informazioni di base** selezionare il gruppo di sottoscrizioni e il gruppo di risorse per cui si vuole creare il gruppo di app. È anche possibile scegliere di creare un nuovo gruppo di risorse anziché selezionarne uno esistente.

5. Selezionare il pool di host che verrà associato al gruppo di applicazioni dal menu a discesa accanto a **Pool di host**.

    >[!NOTE]
    >È necessario selezionare il pool di host associato al gruppo di applicazioni. I gruppi di app hanno app o desktop gestiti da un host di sessione e gli host di sessione fanno parte dei pool di host. Il gruppo di app deve essere associato a un pool di host durante la creazione.

    > [!div class="mx-imgBorder"]
    > ![Screenshot della scheda Informazioni di base nel portale di Azure.](media/basics-tab.png)

6. Se si vogliono aggiungere gruppi di applicazioni al pool di host, selezionare **Pool di host** nel menu sul lato sinistro della schermata.
   
    Selezionare quindi il nome del pool di host a cui si vogliono aggiungere i gruppi di applicazioni.
   
    Successivamente, selezionare **Gruppi di applicazioni** nel menu sul lato sinistro della schermata, quindi selezionare **+ Aggiungi**.

    Infine, selezionare il gruppo di sottoscrizioni e il gruppo di risorse in cui si vuole creare il gruppo di app. È possibile selezionare il nome di un gruppo di risorse esistente dal menu a discesa o selezionare **Crea nuovo** per creare un nuovo gruppo di risorse.

      >[!NOTE]
      >Quando si aggiungono gruppi di applicazioni al pool di host, il pool di host correlato al gruppo di applicazioni è già selezionato perché è da tale gruppo che è iniziata la selezione.
      > 
      > [!div class="mx-imgBorder"]
      >![Screenshot della scheda Informazioni di base con il pool di host preselezionato.](media/host-pool-selected.png)

7. Selezionare **RemoteApp** in Tipo di gruppo di applicazioni, quindi immettere un nome per RemoteApp.

      > [!div class="mx-imgBorder"]
      > ![Screenshot dei campi di Tipo di gruppo di applicazioni. "RemoteApp" è evidenziato.](media/remoteapp-button.png)

8.  Selezionare la scheda **Assegnazioni**.

9.  Per pubblicare singoli utenti o gruppi di utenti nel gruppo di app, selezionare **+ Aggiungi utenti o gruppi di utenti di Azure AD**.

10.  Selezionare il numero di utenti a cui si vogliono aggiungere le app. È possibile selezionare uno o più utenti e gruppi di utenti.

     > [!div class="mx-imgBorder"]
     > ![Screenshot del menu di selezione dell'utente.](media/select-users.png)

11.  Scegliere **Seleziona**.

12.  Selezionare la scheda **Applicazioni**, quindi selezionare **+ Aggiungi applicazioni**.

13.  Per aggiungere un'applicazione dal menu Start: 

      - Passare a **Origine applicazione** e selezionare **menu Start** dal menu a discesa. Passare quindi ad **Applicazione** e scegliere l'applicazione dal menu a discesa.

     > [!div class="mx-imgBorder"]
     > ![Screenshot della schermata Aggiungi applicazione con il menu Start selezionato.](media/add-app-start.png)

      - In **Nome visualizzato** immettere il nome dell'applicazione che verrà visualizzato dall'utente nel client.

      - Lasciare invariate le altre opzioni e selezionare **Salva**.

14. Per aggiungere un'applicazione da un percorso di file specifico:

      - Passare a **Origine applicazione** e selezionare **Percorso file** dal menu a discesa.

      - Immettere il percorso dell'applicazione nell'host di sessione, registrato con il pool di host associato.

      - Immettere i dettagli dell'applicazione nei campi **Nome applicazione**, **Nome visualizzato**, **Percorso icone** e **Indice icone**.

      - Selezionare **Salva**.

     > [!div class="mx-imgBorder"]
     > ![Screenshot della pagina Aggiungi applicazione con il percorso file selezionato.](media/add-app-file.png)

     Ripetere questo processo per tutte le applicazioni che si vuole aggiungere al gruppo di applicazioni.

15.  Selezionare quindi la scheda **Area di lavoro**.

16.  Se si vuole registrare il gruppo di app in un'area di lavoro, passare a **Registra gruppo di applicazioni** e selezionare **Sì**. Se si preferisce registrare il gruppo di app in un secondo momento, selezionare **No**.

17.  Se si seleziona **Sì**, è possibile selezionare un'area di lavoro esistente in cui registrare il gruppo di app.
       
       >[!NOTE]
       >È possibile registrare il gruppo di app solo nelle aree di lavoro create nella stessa posizione del pool di host. Inoltre, se in precedenza è stato registrato un altro gruppo di app dallo stesso pool di host del nuovo gruppo di app in un'area di lavoro, questo verrà selezionato e non sarà possibile modificarlo. Tutti i gruppi di app di un pool di host devono essere registrati nella stessa area di lavoro.

     > [!div class="mx-imgBorder"]
     > ![Screenshot della pagina Registra gruppo di applicazioni per un'area di lavoro già esistente. Il pool di host è preselezionato.](media/register-existing.png)

18. Facoltativamente, se si vogliono creare tag per semplificare l'organizzazione dell'area di lavoro, selezionare la scheda **Tag** e immettere i nomi dei tag.

19. Al termine, selezionare la scheda **Rivedi e crea**.

20. Attendere il completamento del processo di convalida. Al termine, selezionare **Crea** per distribuire il gruppo di app.

Il processo di distribuzione eseguirà le operazioni seguenti:

- Creare il gruppo di app RemoteApp.
- Aggiungere le app selezionate al gruppo di app.
- Pubblicare il gruppo di app pubblicato per gli utenti e i gruppi di utenti selezionati.
- Registrare il gruppo di app, se si è scelto di eseguire questa operazione.
- Creare un collegamento a un modello di Azure Resource Manager in base alla configurazione che è possibile scaricare e salvare per un uso successivo.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come creare un gruppo di app, popolarlo con programmi RemoteApp e assegnare utenti. Per informazioni su come creare un pool di host di convalida, vedere l'esercitazione seguente. È possibile usare un pool di host di convalida per monitorare gli aggiornamenti del servizio prima di distribuirli nell'ambiente di produzione.

> [!div class="nextstepaction"]
> [Creare un pool di host per convalidare gli aggiornamenti dei servizi](./create-validation-host-pool.md)
