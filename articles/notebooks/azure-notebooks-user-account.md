---
title: Accedere ad Azure Notebooks
description: Configurare l'account utente per Azure Notebooks usando un account Microsoft o un account aziendale o dell'istituto di istruzione.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 0d657fcc-26bc-41dd-abf0-3e5cfd66e0e0
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: b8e5c5b14ecdbc63daf200b7d11e755822cd063b
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59257009"
---
# <a name="your-user-account-for-azure-notebooks"></a>L'account utente per Azure Notebooks

È possibile usare Azure Notebooks anche senza aver eseguito l'accesso con un account utente:

- Se non si esegue l'accesso, è possibile creare ed eseguire i notebook ma non è possibile conservare i notebook o i file di dati nell'ambito dei progetti. Gli utenti che ricevono un collegamento a un notebook di Azure, ad esempio, possono accedere al notebook senza dover eseguire l'accesso.
- Se si esegue l'accesso, Azure Notebooks mantiene tutti i progetti associati al proprio account. Agli utenti viene inoltre assegnato un ID utente che consente di condividere i progetti e i notebook con altre persone.
  - Se l'account usato per Azure Notebooks è associato anche a una sottoscrizione di Azure, è possibile ottenere vantaggi aggiuntivi, come la possibilità di eseguire i notebook in server più potenti, creare notebook privati e concedere autorizzazioni per i notebook a singoli utenti.

L'accesso ad Azure Notebooks richiede un account Microsoft o un account aziendale o dell'istituto di istruzione. Viene richiesto di immettere le credenziali dell'account quando si seleziona il comando **Accedi** nell'angolo superiore destro della pagina di Notebooks:

![Comando di accesso ad Azure Notebooks](media/accounts/sign-in-command.png)

Tutte le operazioni effettuate in Azure Notebooks sono associate all'account usato per eseguire l'accesso. Nel [profilo utente](azure-notebooks-user-profile.md) di ogni account deve essere presente anche un ID utente univoco. È quindi possibile accedere ad Azure Notebooks con account diversi se è necessario conservare identità e set di progetti separati. Ogni membro di un team di data science, ad esempio, può avere sia un account individuale sia un account di gruppo condiviso da usare per presentare il proprio lavoro a persone esterne all'azienda. Analogamente, gli istruttori possono mantenere un account associato al proprio ruolo di docente diverso da quello usato per consulenze esterne o per il lavoro open source.

## <a name="microsoft-accounts"></a>Account Microsoft

Gli account Microsoft consentono di accedere a qualsiasi numero di prodotti e servizi Microsoft, ad esempio Windows, Azure, outlook.com, OneDrive e XBox Live. Se si usa uno di questi servizi, è probabile che si disponga già di un account Microsoft che può essere usato anche con Azure Notebooks.

Se non se ne è certi, selezionare il comando **Create one** (Creane uno) nel prompt di account. È possibile creare un nuovo account Microsoft usando qualsiasi indirizzo di posta elettronica di qualunque provider.

![Comando per la creazione di un nuovo account Microsoft](media/accounts/create-new-microsoft-account.png)

In caso di account per bambini, l'accesso ad Azure Notebooks è bloccato per impostazione predefinita. Se si accede con un account per bambini, viene visualizzato l'errore seguente:

![Errore durante il tentativo di accedere con un account per bambini: si è verificato un problema, uno dei tuoi genitori ha bloccato l'accesso](media/accounts/child-account-error.png)

Per abilitare l'accesso, un genitore deve seguire questa procedura:

1. Visitare `https://account.live.com/mk` e accedere con un account padre.
1. Nella sezione del bambino selezionare **Gestisci l'accesso del bambino alle app di terze parti**.
1. Nella pagina successiva, selezionare **Abilita l'accesso**.
1. La volta successiva che l'account per bambini verrà usato per accedere ad Azure Notebooks, sarà sufficiente selezionare **Sì** nel prompt delle autorizzazioni visualizzato.

> [!Warning]
> Se si abilita l'accesso ad app di terze parti per Azure Notebooks, si abilita automaticamente l'accesso anche per tutte le altre app di terze parti. I genitori devono quindi prestare attenzione quando abilitano l'accesso e monitorare da vicino l'attività del figlio.

## <a name="work-or-school-accounts"></a>Account aziendali o dell'istituto di istruzione

Un amministratore dell'organizzazione crea un account aziendale o dell'istituto di istruzione per consentire ai membri dell'organizzazione di accedere ai servizi cloud Microsoft (come Office 365), oltre a un account per accedere a Windows in un computer aggiunto al dominio. In genere, un account aziendale o dell'istituto di istruzione usa un indirizzo di posta elettronica dell'organizzazione, ad esempio any-user@contoso.com.

L'accesso ad Azure Notebooks con un account aziendale o dell'istituto di istruzione può richiedere il consenso dell'amministratore perché Azure Notebooks raccoglie o usa (ma non divulga) informazioni come l'indirizzo di posta elettronica dell'account e il browser dell'utente (i dati del browser consentono di ottimizzare le funzionalità in base all'utilizzo comune).

L'amministratore di un account aziendale deve fornire il consenso per conto degli utenti se questi non possono farlo individualmente. In questo caso, agli utenti verrà visualizzato il messaggio "Non è possibile accedere all'applicazione":

![Messaggio "Non è possibile accedere all'applicazione" quando si usa un account aziendale o dell'istituto di istruzione](media/accounts/consent-permissions-denied.png)

Per fornire il consenso come amministratore, usare la [pagina del consenso dell'amministratore](https://notebooks.azure.com/account/adminConsent) in cui è possibile completare il processo.

## <a name="next-steps"></a>Passaggi successivi  

> [!div class="nextstepaction"]
> [Modificare il profilo e un ID utente](azure-notebooks-user-profile.md)
