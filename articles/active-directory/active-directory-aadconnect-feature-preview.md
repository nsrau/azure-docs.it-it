<properties
   pageTitle="Funzionalità in anteprima di Azure AD Connect | Microsoft Azure"
   description="In questo argomento vengono descritte in maggiore dettaglio le funzionalità in anteprima in Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"  
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="01/21/2016"
   ms.author="andkjell;billmath"/>

# Altre informazioni sulle funzionalità in anteprima
In questo argomento viene descritto come usare le funzionalità attualmente in anteprima.

## Estensioni della directory
Le estensioni della directory consentono di estendere lo schema in Azure AD con attributi personalizzati da Active Directory in locale. Questo consentirà di compilare app line-of-business che utilizzano attributi che si continua a gestire in locale. Questi attributi possono essere utilizzati tramite le [estensioni della directory Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) o [Microsoft Graph](https://graph.microsoft.io/). È possibile visualizzare gli attributi disponibili usando rispettivamente lo [strumento di esplorazione di Azure AD Graph](https://graphexplorer.cloudapp.net) e lo [strumento di esplorazione di Microsoft Graph](https://graphexplorer2.azurewebsites.net/).

Attualmente nessun carico di lavoro di Office 365 utilizza questi attributi.

Durante l'installazione di Azure AD Connect, verrà registrata un'applicazione dove saranno disponibili questi attributi. È possibile visualizzare questa applicazione nel portale di Azure. ![App estensione dello schema](./media/active-directory-aadconnect-feature-preview/extension3.png)

Questi attributi ora saranno disponibili tramite Graph: ![Grafico](./media/active-directory-aadconnect-feature-preview/extension4.png)

Gli attributi sono preceduti da extension\_{AppClientId}\_. AppClientId avrà lo stesso valore per tutti gli attributi della directory Azure AD.

Sono supportati solo gli attributi a valore singolo e il valore negli attributi non può superare i 250 caratteri.

## Writeback dei gruppi
L'opzione per il writeback dei gruppi nelle funzionalità facoltative consentirà il writeback dei "gruppi di Office 365" in una foresta in cui è installato Exchange. Si tratta di un gruppo che viene sempre gestito nel cloud. Se Exchange è disponibile in locale, è possibile eseguire il writeback di questi gruppi in locale in modo che gli utenti con una cassetta postale di Exchange locale possano inviare e ricevere messaggi di posta elettronica da questi gruppi.

Altre informazioni sui gruppi di Office 365 e su come usarli sono disponibili [qui](http://aka.ms/O365g).

Questo gruppo verrà rappresentato come gruppo di distribuzione in AD DS locale. Per riconoscere questo nuovo tipo di gruppo, nel server di Exchange locale deve essere installato l'aggiornamento cumulativo 8 di Exchange 2013 (rilasciato a marzo 2015) o Exchange 2016.

**Note durante l'anteprima**

- Attualmente l'attributo della rubrica non è popolato nell'anteprima. Senza questo attributo, il gruppo non sarà visibile nell'elenco indirizzi globale. Il modo più semplice per popolare questo attributo consiste nell'usare il cmdlet di PowerShell per Exchange `update-recipient`.
- Solo le foreste con lo schema di Exchange sono destinazioni valide per i gruppi. Se è stata rilevata alcuna versione di Exchange, il writeback dei gruppi non potrà essere abilitato.
- Attualmente sono supportate solo le distribuzioni in organizzazioni di Exchange a foresta singola. Se sono presenti più organizzazioni di Exchange locali, sarà necessaria una soluzione GALSync locale per visualizzare questi gruppi nelle altre foreste.
- La funzionalità di writeback dei gruppi al momento non gestisce gruppi di protezione o gruppi di distribuzione.

>[AZURE.NOTE] Una sottoscrizione di Azure AD Premium è necessaria per il writeback dei gruppi.

## Writeback degli utenti
> [AZURE.IMPORTANT] La funzionalità di anteprima di writeback degli utenti è stata temporaneamente rimossa nell'aggiornamento di agosto 2015 di Azure AD Connect. Se questa funzionalità è stata abilitata, è necessario disabilitarla.

La funzionalità di writeback degli utenti è in una fase iniziale di anteprima. Può essere usata solo quando l'origine per tutti gli oggetti utente è Azure AD e la directory Active Directory locale è vuota prima di abilitare la funzionalità (distribuzione vergine).

> [AZURE.WARNING] Questa funzionalità dovrebbe essere valutata solo in un ambiente di test e non deve essere usata in una directory Azure AD destinata all'utilizzo in produzione.

.

>[AZURE.NOTE] Una sottoscrizione di Azure AD Premium è necessaria per il writeback degli utenti.

## Passaggi successivi
Continuare l'[Installazione personalizzata di Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0128_2016-->