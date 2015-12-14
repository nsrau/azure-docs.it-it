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
   ms.date="12/02/2015"
   ms.author="andkjell;billmath"/>

# Altre informazioni sulle funzionalità in anteprima
In questo argomento viene descritto come usare le funzionalità attualmente in anteprima.

## Writeback degli utenti
> [AZURE.IMPORTANT]La funzionalità di anteprima di writeback degli utenti è stata temporaneamente rimossa nell'aggiornamento di agosto di AAD Connect. Se questa funzionalità è stata abilitata, è necessario disabilitarla.

La funzionalità di writeback degli utenti è in una fase iniziale di anteprima. Può essere usata solo quando l'origine per tutti gli oggetti utente è Azure AD e Active Directory in locale è vuota prima di abilitare la funzionalità.

> [AZURE.IMPORTANT]Questa funzionalità dovrebbe essere verificata solo in un ambiente di test e non deve essere usata in una directory Azure AD destinata all'utilizzo in produzione.

## Writeback dei gruppi
L'opzione per il writeback dei gruppi nelle funzionalità facoltative consentirà il writeback dei "gruppi di Office 365" in una foresta in cui è installato Exchange. Si tratta di un nuovo tipo di gruppo che viene sempre utilizzato nel cloud. È disponibile in outlook.office365.com o su myapps.microsoft.com, come illustrato di seguito:


![Filtro sincronizzazione](./media/active-directory-aadconnect-feature-preview/office365.png)

![Filtro sincronizzazione](./media/active-directory-aadconnect-feature-preview/myapps.png)

Questo gruppo verrà rappresentato come gruppo di distribuzione in AD DS locale. Per riconoscere questo nuovo tipo di gruppo, il server di Exchange locale deve disporre dell’aggiornamento cumulativo 8 di Exchange 2013 (rilasciato a marzo 2015).

**Nota**

- Attualmente l'attributo della rubrica non è popolato nell'anteprima. Il modo più semplice per eseguire questa operazione consiste nell'usare il cmdlet di PowerShell per Exchange update-recipient.
- Solo le foreste con lo schema di Exchange sono destinazioni valide per i gruppi. Se è stata rilevata alcuna versione di Exchange, il writeback dei gruppi non potrà essere abilitato.
- La funzionalità di writeback dei gruppi al momento non gestisce gruppi di protezione o gruppi di distribuzione.

Altre informazioni sui gruppi di Office 365 sono disponibili [qui](http://aka.ms/O365g).

## Estensioni della directory
Le estensioni della directory consentono di estendere lo schema in Azure AD con attributi personalizzati da Active Directory in locale.

Sono supportati solo gli attributi a valore singolo e il valore negli attributi non può superare i 250 caratteri. Il metaverse e lo schema Azure AD verranno estesi con gli attributi selezionati. In Azure AD viene aggiunta una nuova applicazione con gli attributi.

![Filtro sincronizzazione](./media/active-directory-aadconnect-feature-preview/extension3.png)

Questi attributi ora saranno disponibili tramite Graph:

![Filtro sincronizzazione](./media/active-directory-aadconnect-feature-preview/extension4.png)

## Passaggi successivi
Continuare l'[Installazione personalizzata di Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_1203_2015-->