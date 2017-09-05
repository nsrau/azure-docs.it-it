---
title: Come usare il pacchetto di contenuto Power BI di Azure Active Directory | Microsoft Docs
description: Informazioni su come usare il pacchetto di contenuto Power BI di Azure Active Directory
services: active-directory
author: MarkusVi
manager: femila
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 5c642bb814a279756e8391f12fdc86b6ec0b4a8f
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="how-to-use-the-azure-active-directory-power-bi-content-pack"></a>Come usare il pacchetto di contenuto Power BI di Azure Active Directory

Comprendere in che modo gli utenti adottano e usano le funzionalità di Azure Active Directory è fondamentale per l'amministratore IT. Consente di pianificare l'infrastruttura IT e la comunicazione per incrementare l'utilizzo e sfruttare al meglio le funzionalità di AAD. Il pacchetto di contenuto Power BI per Azure Active Directory consente di analizzare ulteriormente i dati per comprendere come usarli per ottenere informazioni più approfondite sulle attività svolte nell'istanza di Azure Active Directory per le varie funzionalità sulle quali si fa particolare affidamento.  Con l'integrazione delle API di Azure Active Directory in Power BI, è possibile scaricare facilmente i pacchetti di contenuto preesistenti e ottenere informazioni dettagliate per tutte le attività all'interno di Azure Active Directory usando l'esperienza di visualizzazione avanzata offerta da Power BI. È possibile creare dashboard personalizzati e condividerli con altri utenti nell'organizzazione. 

Questo argomento offre istruzioni dettagliate su come installare e usare il pacchetto di contenuto nel proprio ambiente.

## <a name="installation"></a>Installare  

**Per installare il pacchetto di contenuto di Power BI:**

1. Accedere a [Power BI](https://app.powerbi.com/groups/me/getdata/services) con l'account di Power BI, ovvero lo stesso account di Office 365 o Azure AD.

2. Selezionare **Recupera dati** nella parte inferiore del riquadro di spostamento a sinistra.

    ![Pacchetto di contenuto Power BI di Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/01.png)
 
3. Nella casella **Servizi** fare clic su **Ottieni**.
   
    ![Pacchetto di contenuto Power BI di Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/02.png)

4.  Cercare **Azure Active Directory**.

    ![Pacchetto di contenuto Power BI di Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/03.png)
 
5.  Quando richiesto, digitare l'ID tenant di Azure AD e quindi fare clic su **Avanti**.

    > [!TIP] 
    > Un modo rapido per ottenere l'ID del tenant di Office 365 / Azure AD consiste nell'accedere al portale di Azure AD, eseguire il drill-down fino alla directory e copiare l'ID dall'URL seguente: https://manage.windowsazure.com/woodgroveonline.com#Workspaces/ActiveDirectoryExtension/Directory/<tenantid>/directoryQuickStart

    ![Pacchetto di contenuto Power BI di Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/04.png) 

6.  Fare clic su **Accedi**. 
 
    ![Pacchetto di contenuto Power BI di Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/05.png) 



7.  Immettere il nome utente e la password, quindi fare clic su **Accedi**.
 
    ![Pacchetto di contenuto Power BI di Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/06.png) 

8.  Nella finestra di dialogo di consenso dell'app, fare clic su **Accetta**.
 
9.  Fare clic sul dashboard dei log attività di Azure Active Directory, dopo che è stato creato.
 
    ![Pacchetto di contenuto Power BI di Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/08.png) 

## <a name="what-can-i-do-with-this-content-pack"></a>Operazioni eseguibili con il pacchetto di contenuto

Prima di esaminare le operazioni eseguibili con questo pacchetto di contenuto, verrà illustrata una breve anteprima dei diversi report presenti nel pacchetto. I dati dei report si riferiscono agli **ultimi 30 giorni**.

### <a name="reports-included-in-this-version-of-azure-active-directory-logs-content-pack"></a>Report inclusi in questa versione del pacchetto di contenuto dei log di Azure Active Directory

**Report di utilizzo e tendenza delle app**: informazioni approfondite sulle app usate nell'organizzazione e su quali vengono usate più frequentemente e quando. Con questo report è possibile ottenere informazioni dettagliate sull'uso di un'app implementata di recente nell'organizzazione oppure scoprire quali app vengono usate maggiormente. In questo modo è possibile migliorare l'utilizzo se emerge che l'app non viene usata.

**Accessi in base a posizioni e utenti**: informazioni dettagliate su tutti gli accessi eseguiti con l'identità di Azure e sull'identità degli utenti. È così possibile esaminare in dettaglio i singoli accessi e rispondere a domande come:

- Da dove ha effettuato l'accesso questo utente?
- Quale utente ha il maggior numero di accessi e da dove accede? 
- L'accesso è stato eseguito correttamente?  
 
È possibile esaminare i dettagli facendo clic su una data o posizione specifica.

**Utenti univoci per ogni app**: ottenere una visualizzazione di tutti gli utenti univoci che usano un'app specifica. Comprende solo gli utenti che hanno completato "*correttamente*" l'accesso a un'applicazione.

**Accessi dei dispositivi**: visualizzazione del tipo del sistema operativo e dei browser usati dagli utenti dell'organizzazione con informazioni dettagliate sugli utenti stessi, tra cui:

- User Name
- Indirizzo IP
- Località 
- Stato accesso 

Con questo report è possibile comprendere i diversi profili dei dispositivi usati all'interno dell'organizzazione e determinare i criteri dei dispositivi in base alle funzioni usate

**Grafico a imbuto SSPR**: informazioni su come vengono reimpostate le password all'interno dell'organizzazione. È possibile ottenere informazioni sul numero di reimpostazioni delle password tentate tramite lo strumento SSPR e quante di esse hanno avuto esito positivo. Analizzare gli errori di reimpostazione delle password usando il grafico a imbuto SSPR e comprendere perché si sono verificati determinati errori. Questo report offre una comprensione più approfondita della modalità d'uso dello strumento SSPR all'interno dell'organizzazione in modo da prendere decisioni efficaci.

## <a name="customizing-azure-ad-activity-content-pack"></a>Personalizzazione del pacchetto di contenuto per le attività di Azure AD

**Modificare la visualizzazione**: è possibile modificare la visualizzazione di un report facendo clic su **Modifica Report** e selezionando la visualizzazione desiderata.
 
![Pacchetto di contenuto Power BI di Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/09.png) 
 
![Pacchetto di contenuto Power BI di Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/10.png) 

**Aggiungere altri campi**: è possibile aggiungere o rimuovere un campo per il report selezionando l'oggetto visivo per il quale si intende aggiungere o rimuovere il campo. Nell'esempio seguente viene aggiunto il campo "Stato accesso" alla vista tabella. 
 
![Pacchetto di contenuto Power BI di Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/11.png) 

**Aggiungere visualizzazioni al dashboard**: è possibile personalizzare il dashboard aggiungendo visualizzazioni personalizzate del report. Nell'esempio seguente, un nuovo filtro denominato "Stato di accesso" è stato aggiunto e incluso nel report. È stata anche modificata la visualizzazione da grafico a barre a grafico a linee. Questo nuovo oggetto visivo può essere aggiunto al dashboard.

![Pacchetto di contenuto Power BI di Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/12.png) 

![Pacchetto di contenuto Power BI di Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/13.png) 
 

 


**Condivisione del dashboard**: dopo aver creato il contenuto desiderato, è possibile condividere il dashboard con gli utenti dell'organizzazione. Si noti che quando si condivide il report, gli utenti possono vedere i campi selezionati nel report stesso.
 
![Pacchetto di contenuto Power BI di Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/14.png) 



## <a name="scheduling-a-daily-refresh-of-your-power-bi-report"></a>Pianificazione di un aggiornamento giornaliero dei report di Power BI

Per pianificare un aggiornamento giornaliero dei report di Power BI, passare a **Set di dati > Impostazioni > Pianifica aggiornamenti** e procedere come illustrato di seguito.
 
![Pacchetto di contenuto Power BI di Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/15.png) 

## <a name="updating-to-newer-version-of-content-pack"></a>Aggiornamento alla versione più recente del pacchetto di contenuto

Se si vuole aggiornare il pacchetto di contenuto per ottenere una versione più recente:

- Scaricare il nuovo pacchetto di contenuto e configurarlo in base alle istruzioni riportate in questo articolo.

- Dopo aver configurato il pacchetto, passare a **Origine dati > Impostazioni > Credenziali origine dati** e immettere nuovamente le credenziali come illustrato di seguito

    ![Pacchetto di contenuto Power BI di Azure Active Directory](./media/active-directory-reporting-power-bi-content-pack-how-to/16.png) 

Quando la nuova versione del pacchetto di contenuto è pronta per l'uso, è possibile rimuovere la versione precedente, se necessario, eliminando i report e i set di dati sottostanti associati.

## <a name="still-having-issues"></a>Se i problemi persistono 

Vedere la [Guida per la risoluzione dei problemi](active-directory-reporting-troubleshoot-content-pack.md). Per informazioni generali su Power BI, vedere questi [articoli della Guida](https://powerbi.microsoft.com/en-us/documentation/powerbi-service-get-started/).
 

## <a name="next-steps"></a>Passaggi successivi

Per una panoramica della creazione di report, vedere [Creazione di report in Azure Active Directory](active-directory-reporting-azure-portal.md).

