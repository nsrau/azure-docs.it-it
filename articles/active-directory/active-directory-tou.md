---
title: Azure Active Directory Terms of Use (Condizioni per l'utilizzo di Azure Active Directory)| Microsoft Docs
description: Azure Active Directory Terms of Use (Condizioni per l'utilizzo di Azure Active Directory) consente a un'azienda di specificare condizioni per l'utilizzo agli utenti dei servizi di Azure AD.
services: active-directory
author: billmath
manager: mtillman
editor: ''
ms.assetid: d55872ef-7e45-4de5-a9a0-3298e3de3565
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/29/2018
ms.author: billmath
ms.openlocfilehash: ea68bad3a2c5e905ccf705404dff0049b451268e
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2018
---
# <a name="azure-active-directory-terms-of-use-feature"></a>Funzione Azure Active Directory Terms of Use (Condizioni per l'utilizzo di Azure Active Directory)
Azure Active Directory Terms of Use (Condizioni per l'utilizzo di Azure Active Directory) offre un sistema semplice che le organizzazioni possono usare per presentare le informazioni agli utenti finali.  In questo modo si garantisce che gli utenti vedano le dichiarazioni rilevanti di non responsabilità che si riferiscono ai requisiti legali o di conformità.

Il contenuto di Azure Active Directory Terms of Use (Condizioni per l'utilizzo di Azure Active Directory) viene presentato in formato pdf.   Il contenuto del documento pdf può essere di qualsiasi tipo, ad esempio documenti di contratti esistenti. Questo consente di acquisire il consenso degli utenti finali durante l'accesso.  È possibile usare le condizioni per l'utilizzo per applicazioni, gruppi di utenti o, se si dispone di più condizioni per l'utilizzo, per scopi diversi.

La parte rimanente di questo documento descrive come usare Azure Active Directory Terms of Use (Condizioni per l'utilizzo di Azure Active Directory).  

## <a name="why-use-azure-ad-terms-of-use"></a>Perché usare Azure Active Directory Terms of Use (Condizioni per l'utilizzo di Azure Active Directory)
Talvolta risulta difficile fare accettare le condizioni per l'utilizzo ai dipendenti o agli ospiti prima dell'accesso. Anche scoprire chi ha accettato le condizioni per l'utilizzo dell'azienda e chi invece non l'ha fatto può non essere facile.  Azure Active Directory Terms of Use (Condizioni per l'utilizzo di Azure Active Directory) offre un sistema semplice che le organizzazioni possono usare per presentare le informazioni agli utenti finali.  In questo modo si garantisce che gli utenti vedano le dichiarazioni rilevanti di non responsabilità che si riferiscono ai requisiti legali o di conformità.

Azure Active Directory Terms of Use (Condizioni per l'utilizzo di Azure Active Directory) può essere usato negli scenari seguenti:
-   Condizioni per l'utilizzo generali per tutti gli utenti dell'organizzazione.
-   Condizioni per l'utilizzo specifiche basate sugli attributi di un utente (ad esempio, medici e infermieri o dipendenti nazionali e internazionali) create da [gruppi dinamici](https://azure.microsoft.com/updates/azure-active-directory-dynamic-membership-for-groups).
-   Condizioni per l'utilizzo specifiche basate sull'accesso ad app a elevato impatto aziendale, come Salesforce.


## <a name="prerequisites"></a>prerequisiti
Seguire questa procedura per configurare Azure Active Directory Terms of Use (Condizioni per l'utilizzo di Azure Active Directory):

1. Accedere ad Azure AD usando un amministratore globale, l'amministratore della sicurezza o un amministratore di accesso condizionale per la directory in cui si desidera configurare Azure Active Directory Terms of Use (Condizioni per l'utilizzo di Azure Active Directory).
2. Verificare che la directory abbia una sottoscrizione di Azure AD Premium P1, P2, EMS E3 o EMS E5.  In caso contrario, [ottenere Azure AD Premium](active-directory-get-started-premium.md) o [avviare una versione di valutazione](https://azure.microsoft.com/trial/get-started-active-directory/).
3. Visualizzare il dashboard di Azure Active Directory Terms of Use (Condizioni per l'utilizzo di Azure Active Directory) all'indirizzo [https://aka.ms/catou](https://aka.ms/catou).

>[!IMPORTANT]
>L'applicazione dei controlli dei criteri di accesso condizionale (incluse le condizioni per l'utilizzo) non è supportata per account di servizio.  È consigliabile escludere tutti gli account di servizio dai criteri di accesso condizionale.

## <a name="add-company-terms-of-use"></a>Aggiungere le condizioni per l'utilizzo dell'azienda
Dopo avere completato le condizioni per l'utilizzo, seguire questa procedura per aggiungerle.

### <a name="to-add-terms-of-use"></a>Per aggiungere le condizioni per l'utilizzo
1. Passare al dashboard all'indirizzo [https://aka.ms/catou](https://aka.ms/catou)
2. Fare clic su Aggiungi.</br>
![Aggiungere le condizioni per l'utilizzo](media/active-directory-tou/tou12.png)
3. Immettere il **Nome** per le condizioni per l'utilizzo
4. Immettere il **Nome visualizzato**.  Questa intestazione è ciò che vedono gli utenti quando effettuano l'accesso.
5. Fare clic su **Sfoglia** per trovare il pdf delle condizioni per l'utilizzo completate e selezionarlo.  La dimensione consigliata per i caratteri è 24.
6. **Selezionare** una lingua per le condizioni d'uso.  L'opzione relativa alla lingua consente di caricare più versioni delle condizioni d'uso, ognuna in una lingua diversa.  La versione visualizzata all'utente finale dipenderà dalle preferenze del browser.
7. Attivare o disattivare l'opzione **Richiedi agli utenti di espandere le Condizioni d'uso**.  Se questa opzione è attivata, agli utenti finali verrà richiesto di visualizzare le condizioni d'uso prima dell'accettazione.
8. In **Accesso condizionale** è possibile **applicare** le condizioni per l'utilizzo caricate selezionando un modello dall'elenco a discesa o criteri di accesso condizionale personalizzati.  I criteri personalizzati di accesso condizionale consentono condizioni per l'utilizzo granulari, fino a un'applicazione cloud o a un gruppo di utenti specifici.  Per altre informazioni, vedere [configurazione dei criteri di accesso condizionale](active-directory-conditional-access-best-practices.md)
9. Fare clic su **Crea**.
10. Se è stato selezionato un modello personalizzato di accesso condizionale, viene visualizzata una nuova schermata che consente di personalizzare il criterio dell'autorità di certificazione.
11. A questo punto dovrebbero essere visualizzate le nuove condizioni per l'utilizzo.</br>

![Aggiungere le condizioni per l'utilizzo](media/active-directory-tou/tou3.png)

## <a name="delete-terms-of-use"></a>Eliminare le condizioni per l'utilizzo
È possibile rimuovere o eliminare le condizioni per l'utilizzo obsolete con questa procedura:

### <a name="to-delete-terms-of-use"></a>Per eliminare le condizioni per l'utilizzo
1. Passare al dashboard all'indirizzo [https://aka.ms/catou](https://aka.ms/catou)
2. Selezionare le condizioni per l'utilizzo che si desidera rimuovere.
3. Fare clic su **Elimina**.
4. A questo punto dovrebbero essere visualizzate le nuove condizioni per l'utilizzo.


## <a name="viewing-current-user-status"></a>Visualizzare lo stato corrente degli utenti
Nelle condizioni per l'utilizzo è visualizzato il numero di utenti che hanno accettato o rifiutato.

![Evento di controllo](media/active-directory-tou/tou15.png)

È possibile fare clic sui numeri sotto **accepted** (accettato) o **declined** (rifiutato) per visualizzare lo stato corrente degli utenti.

![Evento di controllo](media/active-directory-tou/tou16.png)

## <a name="audit-terms-of-use"></a>Controllare le condizioni per l'utilizzo
Per visualizzare dati cronologici su chi ha accettato o rifiutato e non solo lo stato corrente, Azure Active Directory Terms of Use (Condizioni per l'utilizzo di Azure Active Directory) fornisce funzioni di controllo facili da usare.  Queste funzioni consentono di vedere chi ha accettato le condizioni per l'utilizzo e quando.  

Esistono due modi in cui è possibile usare il controllo, a seconda di ciò che si intende fare.  


Per iniziare con il controllo, seguire questa procedura:

### <a name="to-audit-terms-of-use"></a>Per controllare le condizioni per l'utilizzo
1. Passare al dashboard all'indirizzo [https://aka.ms/catou](https://aka.ms/catou)
2. Fare clic su Visualizza log di controllo.</br>
![Evento di controllo](media/active-directory-tou/tou8.png)
3.  Nella schermata dei registri di controllo di Azure AD è possibile filtrare le informazioni usando i menu a discesa forniti per visualizzare informazioni specifiche dei registri di controllo.
[Evento di controllo](media/active-directory-tou/tou9.png)
4.  È inoltre possibile scaricare le informazioni in un file con estensione csv per l'utilizzo in locale.

## 

## <a name="what-users-see"></a>Cosa vedono gli utenti
Una volta create e applicate le condizioni per l'utilizzo, agli utenti inclusi nell'ambito verrà visualizzato quanto elencato di seguito.  Gli utenti vedranno queste schermate durante l'accesso.
-   È consigliabile impostare la dimensione del carattere all'interno del PDF su 24.
![Evento di controllo](media/active-directory-tou/tou10.png)
-   Questa schermata è quella visualizzata nei dispositivi mobili</br></br>
![Evento di controllo](media/active-directory-tou/tou11.png)

### <a name="review-terms-of-use"></a>Verificare le condizioni d'uso
Gli utenti possono visualizzare e verificare le condizioni d'uso accettate.  Per verificare le condizioni per l'utilizzo, seguire questa procedura:

1. Passare a [https://myapps.microsoft.com](https://myapps.microsoft.com) ed eseguire l'accesso.
2. Nell'angolo superiore destro fare clic sul nome e selezionare **Profilo** nel menu a discesa.
![Profilo](media/active-directory-tou/tou14.png)

3. In Profilo fare clic su **Verificare le condizioni d'uso**.
![Evento di controllo](media/active-directory-tou/tou13a.png)

4.  Sarà quindi possibile verificare le condizioni per l'utilizzo accettate. 


## <a name="additional-information"></a>Informazioni aggiuntive
Di seguito vengono riportate informazioni che possono risultare utili per l'uso delle condizioni per l'utilizzo.

>[!IMPORTANT]
> Gli utenti inclusi nell'ambito devono disconnettersi e accedere per soddisfare un nuovo criterio se:
> - è abilitato un criterio di accesso condizionale per una condizione per l'utilizzo
> - o viene creata una seconda condizione
>
>I criteri di accesso condizionale diventano effettivi immediatamente. In questo caso l'amministratore inizierà a vedere nuvolette "tristi" o messaggi relativi a problemi di token di Azure AD. Dovrà quindi disconnettersi e accedere nuovamente per soddisfare il nuovo criterio.





## <a name="frequently-asked-questions"></a>Domande frequenti

**D: come posso sapere se un utente ha accettato le condizioni per l'utilizzo?**</br>
R: è possibile fare semplicemente clic sul numero sotto accepted (accettato) accanto alle condizioni per l'utilizzo.  Per altre informazioni, vedere [Visualizzare lo stato corrente degli utenti](#viewing-current-user-status).  Gli utenti che accettano le condizioni per l'utilizzo vengono inoltre riportati nel log di controllo. È quindi possibile ottenere le informazioni desiderate cercando nel log di controllo di Azure AD.  

**D: se i termini delle condizioni per l'utilizzo vengono modificati è necessario che gli utenti le accettino di nuovo?**</br>
R: sì, un amministratore può modificare le condizioni per l'utilizzo rendendone nuovamente necessaria l'accettazione.

**D: le condizioni per l'utilizzo possono supportare più lingue?**</br>
A: Sì.  L'amministratore può attualmente configurare 18 lingue diverse per singole condizioni per l'utilizzo. 

**D: quando vengono attivate le condizioni per l'utilizzo?**</br>
R: le condizioni per l'utilizzo vengono attivate durante l'accesso.

**D: a quali altre applicazioni è possibile applicare le condizioni per l'utilizzo?**</br>
R: è possibile creare un criterio di accesso condizionale per le applicazioni aziendali utilizzando l'autenticazione moderna.  Per altre informazioni, vedere le [applicazioni aziendali](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-view-azure-portal).

**D: è possibile aggiungere più condizioni per l'utilizzo a un utente o a un'app specifici?**</br>
R: sì, mediante la creazione di più criteri di accesso condizionale destinati a tali gruppi o app. Se un utente rientra nell'ambito di più condizioni per l'utilizzo, accetta una condizione alla volta.
 
**D: cosa accade se un utente rifiuta le condizioni per l'utilizzo?**</br>
R: ne viene bloccato l'accesso all'applicazione. L'utente deve accedere nuovamente e accettare le condizioni.
