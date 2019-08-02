---
title: Come creare un account Marketplace commerciale nel centro per i partner
description: Informazioni su come creare un account Marketplace commerciale nel centro per i partner.
author: mattwojo
manager: evansma
ms.author: parthp
ms.service: marketplace
ms.topic: guide
ms.date: 07/05/2019
ms.openlocfilehash: 16ed52aa2b56a97469ae8fc47299f09593b19c45
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668335"
---
# <a name="create-a-commercial-marketplace-account-in-partner-center"></a>Creare un account Marketplace commerciale nel centro per i partner

Per pubblicare le offerte in [Azure Marketplace](https://azuremarketplace.microsoft.com/) o in [AppSource](https://appsource.microsoft.com/), è necessario creare un account nel programma commerciale Marketplace nel centro per i partner.

## <a name="create-a-partner-center-account"></a>Creare un account del centro per i partner

In questo articolo verrà illustrato come creare un account del centro per i partner, inclusa la procedura per: 

- [Eseguire la registrazione tramite la pagina di registrazione del centro per i partner](#to-create-a-commercial-marketplace-account-in-partner-center)
- [Accedere con un account aziendale](#sign-in-with-a-work-account)
- [Accettare i termini e le condizioni](#agree-to-terms-and-conditions) 
- [Specificare il profilo di pubblicazione](#provide-your-publisher-profile)

>[!Important]
>Se si dispone di un account nel [portale cloud partner (CPP)](https://cloudpartner.azure.com) che è stato spostato nel centro per i partner, non è necessario creare un nuovo account. Per ulteriori informazioni, vedere [autori che passano da cpp](#publishers-moving-from-cpp) . 

### <a name="before-you-begin"></a>Prima di iniziare

Per creare un account nel centro per i partner, verificare di avere:

- Autorità per la firma di contratti legali per conto dell'azienda.
- Il nome dell'azienda legale, l'indirizzo e il contatto principale dell'azienda (questo può essere l'utente).

Queste informazioni verranno verificate durante il processo di creazione dell'account.

### <a name="to-create-a-commercial-marketplace-account-in-partner-center"></a>Per creare un account Marketplace commerciale nel centro per i partner

Esaminare le informazioni nella pagina [**di registrazione del centro per i partner Microsoft**](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv) e quindi eseguire la registrazione per un account.

#### <a name="sign-in-with-a-work-account"></a>Accedere con un account aziendale

In modo da poter collegare il dominio dell'account di posta elettronica aziendale al nuovo account del centro per i partner. Associando questi account, i dipendenti della società possono accedere al centro per i partner con i nomi utente e le password dell'account di lavoro.

>[!Note]
>Per verificare se la società ha già un account di lavoro, come creare un nuovo account aziendale o come configurare più account di lavoro da usare con il centro per i partner, visitare [l'account aziendale e il centro per i partner](./company-work-accounts.md). 

#### <a name="agree-to-terms-and-conditions"></a>Accetto i termini e le condizioni

È necessario accettare i termini e le condizioni nel [contratto Microsoft Marketplace Publisher](http://go.microsoft.com/fwlink/?LinkID=699560).

#### <a name="provide-your-publisher-profile"></a>Specificare il profilo di pubblicazione

Il profilo di pubblicazione include il nome della società e l'ID MPN. Se non è ancora stato fatto, sarà necessario partecipare al [Microsoft Partner Network](https://partner.microsoft.com/commercial). Dopo aver partecipato alla Microsoft Partner Network, verrà fornito un ID MPN. 

Creare un ID editore. L'ID dell'editore identifica in modo univoco la società e le offerte in Marketplace e AppSource. 

Dopo aver confermato le informazioni sul profilo di pubblicazione, accettare i termini e le condizioni e creare l'account del centro per i partner selezionando **accetta e continua**. 

>[!Important]
>*L'utente deve essere autorizzato a agire per conto dell'azienda per accettare le presenti condizioni.*

Grazie per aver creato un account nel centro per i partner. Si passerà ora alla pagina [Panoramica del Marketplace commerciale](./commercial-marketplace-overview.md) .

### <a name="publishers-moving-from-cpp"></a>Autori che passano da CPP

Se è stata eseguita la migrazione dell'account dalla [portale cloud partner (CPP)](https://cloudpartner.azure.com), non è necessario creare un nuovo account del centro per i partner, ma si riceverà un collegamento personalizzato al nuovo account del centro per i partner tramite posta elettronica e in una notifica di banner visualizzata dopo accesso all'account CPP esistente.

Dopo aver abilitato il nuovo account del centro per i partner visitando questo collegamento personalizzato, è possibile tornare al proprio account visitando il [Dashboard del Marketplace commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/) nel centro per i partner.

Il contratto di pubblicazione e le informazioni sul profilo della società verranno migrati al nuovo account del centro per i partner, insieme a tutte le informazioni sul profilo di pagamento dell'account configurate in precedenza, agli account utente e alle autorizzazioni e alle offerte attive associate all'account CPP. 

Una volta che le informazioni sull'account sono state spostate da CPP al centro per i partner, non verrà più usato CPP per eseguire aggiornamenti dell'account o gestire utenti, autorizzazioni e fatturazione. Per un periodo di tempo limitato, gli aggiornamenti dell'account apportati al centro per i partner verranno automaticamente aggiornati nell'account CPP di sola lettura fino alla fine della deprecazione del portale CPP.

## <a name="add-new-publishers-to-the-commercial-marketplace-program"></a>Aggiungere nuovi editori al programma commerciale Marketplace

Un'organizzazione può avere più editori associati a un account Marketplace commerciale. Un utente esistente può aggiungere altri server di pubblicazione dopo aver eseguito l'accesso al centro per i partner, selezionando **Impostazioni** -> account server di**pubblicazione** -> **Aggiungi server**di pubblicazione.

>[!Note]
>Prima di aggiungere un nuovo server di pubblicazione, è possibile esaminare i server di pubblicazione esistenti accedendo al centro per i partner e selezionando **Impostazioni** -> account**Publishers** per visualizzare un elenco di server di pubblicazione esistenti.

Un altro utente dello stesso tenant di Azure Active Directory può aggiungere un nuovo server di pubblicazione attenendosi alla procedura seguente:

1. Avviare il flusso di iscrizione presso il centro per i [partner Microsoft](https://partner.microsoft.com/en-us/dashboard/account/v3/enrollment/introduction/azureisv).
1. Selezionare **Sign in with a work account** e immettere l'indirizzo di posta elettronica dell'ufficio.
1. Selezionare il pulsante **Aggiungi server di pubblicazione** .
1. Scegliere l'ID MPN che si desidera associare al server di pubblicazione.
1. Aggiornare i **Dettagli del server di pubblicazione** nel form. <br>

   1. **Nome editore**: Nome che verrà visualizzato in Azure Marketplace o AppSource con l'offerta. <br>
   1. **PublisherID**: Identificatore usato dal centro per i partner per identificare in modo univoco l'editore. Il valore predefinito per questo campo è mappato a un `PublisherID` oggetto esistente e univoco nel sistema, che non può essere riutilizzato e pertanto questo campo deve essere aggiornato. <br>
   1. **Informazioni di contatto**: Quando necessario, aggiornare le informazioni di contatto.

1. Dopo aver completato il processo, è possibile gestire il server di pubblicazione appena creato passando al programma **Commercial Marketplace** indicato nel menu di spostamento a sinistra. Se non viene visualizzato il programma **Commercial Marketplace** , aggiornare la pagina.  Il nuovo server di pubblicazione verrà visualizzato nell'elenco **Publishers** .

## <a name="next-steps"></a>Passaggi successivi

- [Gestisci l'account del Marketplace commerciale nel centro per i partner](./manage-account.md) 
