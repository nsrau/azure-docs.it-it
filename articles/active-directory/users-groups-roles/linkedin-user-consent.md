---
title: "Consenso: Azure Active Directory e condivisione dei dati di connessioni dell'account LinkedIn | Microsoft Docs"
description: Spiega in che modo l'integrazione di LinkedIn condivide i dati tramite app Microsoft in Azure Active Directory
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e6e24f4e726e25ae65ecceaeb161f8e16d61721
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58200447"
---
# <a name="linkedin-account-connections-data-sharing-and-consent"></a>Condivisione dei dati connessioni account LinkedIn e consenso

È possibile abilitare gli utenti dell'organizzazione di Active Directory (Azure AD) per fornire il consenso per connettere Microsoft aziendale o dell'istituto di istruzione tramite il proprio account LinkedIn. Dopo che un utente si connette i propri account, informazioni e novità di rilievo da LinkedIn sono disponibili in alcune App e servizi Microsoft. Gli utenti possono anche prevedere che l'esperienza di rete su LinkedIn risulti migliorata e arricchita dalle informazioni provenienti da Microsoft.

Per visualizzare le informazioni di LinkedIn nelle app e nei servizi Microsoft, gli utenti devono consentire alla connessione dei propri account Microsoft e LinkedIn. La richiesta di connessione degli account viene visualizzata la prima volta che gli utenti fanno clic per vedere le informazioni di LinkedIn relative a un utente in una scheda del profilo in Outlook, OneDrive o SharePoint Online. Le connessioni all'account LinkedIn non vengono completamente abilitate fino a quando gli utenti non forniscono il consenso all'esperienza e alla connessione dei propri account.

[!INCLUDE [active-directory-gdpr-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="benefits-of-sharing-linkedin-information"></a>Vantaggi della condivisione di informazioni di LinkedIn

L'accesso alle informazioni di LinkedIn all'interno di app e servizi Microsoft rende più semplice per gli utenti connettersi, comunicare e creare rapporti professionali con colleghi, clienti e partner all'interno e all'esterno dell'organizzazione. I nuovi utenti possono essere operativi più rapidamente collegandosi ai colleghi, ottenendo maggiori informazioni su di loro e accedendo con facilità a informazioni più ampie. Di seguito è riportato un esempio di come le informazioni di LinkedIn vengono visualizzate nella scheda del profilo delle app Microsoft:

![Abilitazione dell'integrazione di LinkedIn dell'organizzazione](./media/linkedin-user-consent/display-example.png)

## <a name="enable-and-announce-linkedin-integration"></a>Abilitare e annunciare l'integrazione di LinkedIn

Per gestire le impostazioni per l'intera organizzazione, è necessario avere i diritti di amministratore di Azure Active Directory. È possibile abilitare le connessioni per tutti gli utenti o per un gruppo specifico di utenti.

1. Per abilitare o disabilitare l'integrazione, seguire i passaggi descritti in [fornire il consenso per l'integrazione di LinkedIn per l'organizzazione AD Azure](linkedin-integration.md).
2. Quando si annuncia l'integrazione di LinkedIn nell'organizzazione, è opportuno invitare gli utenti a consultare le domande frequenti in [Informazioni di LinkedIn nelle app e nei servizi Microsoft](https://support.office.com/article/about-linkedin-information-and-features-in-microsoft-apps-and-services-dc81cc70-4d64-4755-9f1c-b9536e34d381). L'articolo fornisce informazioni utili per sapere dove vengono visualizzate le informazioni di LinkedIn, come connettere gli account e altro ancora.

## <a name="user-consent-for-data-access-in-microsoft-and-linkedin"></a>Consenso utente per l'accesso ai dati in Microsoft e LinkedIn

I dati a cui si accede da LinkedIn non vengono archiviati in modo permanente nei servizi Microsoft. I dati a cui si accede da Microsoft non vengono archiviati in modo permanente in LinkedIn, tranne i contatti. I contatti di Microsoft rimangono archiviati in LinkedIn fino a quando non vengono rimossi, come descritto nella pagina sull'[eliminazione dei contatti importati da LinkedIn](https://www.linkedin.com/help/linkedin/answer/43377).

Quando gli utenti connettono i propri account, le informazioni e gli approfondimenti di LinkedIn sono disponibili in alcune app Microsoft, ad esempio nella scheda del profilo. Gli utenti possono anche prevedere che l'esperienza di rete su LinkedIn risulti migliorata e arricchita dalle informazioni provenienti da Microsoft.
Quando gli utenti dell'organizzazione connettono il proprio account LinkedIn e l'account Microsoft aziendale o dell'istituto di istruzione, hanno due possibilità:

* Concedere l'autorizzazione per l'accesso ai dati da entrambi gli account. In altre parole, gli utenti autorizzano il proprio account Microsoft aziendale ad accedere ai dati del proprio account LinkedIn, nonché [il proprio account LinkedIn ad accedere ai dati dell'account Microsoft aziendale o dell'istituto di istruzione](https://www.linkedin.com/help/linkedin/answer/84077).
* Concedere l'autorizzazione per il solo accesso ai dati di LinkedIn da parte dell'account Microsoft aziendale o dell'istituto di istruzione.

Gli utenti possono scollegare gli account e rimuovere le autorizzazioni di accesso ai dati in qualsiasi momento e [possono controllare la modalità di visualizzazione del proprio profilo LinkedIn](https://www.linkedin.com/help/linkedin/answer/83), stabilendo ad esempio se consentire la visualizzazione del proprio profilo all'interno delle app Microsoft.

### <a name="linkedin-account-data"></a>Dati degli account LinkedIn

Con la connessione degli account Microsoft e LinkedIn si consente a LinkedIn di fornire a Microsoft i dati seguenti:

* Dati del profilo: comprendono l'identità LinkedIn, le informazioni di contatto e le informazioni condivise con altri utenti nel proprio [profilo LinkedIn](https://www.linkedin.com/help/linkedin/answer/15493).
* Dati relativi agli interessi: includono gli interessi in LinkedIn, ad esempio le persone e gli argomenti seguiti, i corsi, i gruppi e i contenuti condivisi e a cui si aggiunge Mi piace.
* Dati sulle sottoscrizioni: includono le sottoscrizioni ad applicazioni e servizi LinkedIn con i dati associati. 
* Dati sulle connessioni: includono la [rete LinkedIn](https://www.linkedin.com/help/linkedin/answer/110) personale compresi i profili e le informazioni di contatto delle connessioni di primo livello.

I dati a cui si accede da LinkedIn non vengono archiviati in modo permanente nei servizi Microsoft. Per altre informazioni sull'uso dei dati personali da parte di Microsoft, vedere l'[Informativa sulla privacy di Microsoft](https://privacy.microsoft.com/privacystatement/).

### <a name="microsoft-work-or-school-account-data"></a>Dati degli account Microsoft aziendali o dell'istituto di istruzione

Con la connessione degli account Microsoft e LinkedIn si consente a Microsoft di fornire a LinkedIn i dati seguenti:

* Dati del profilo: includono informazioni come nome, cognome, foto del profilo, indirizzo di posta elettronica, responsabile e persone gestite.
* Dati del calendario: includono le riunioni impostate nei calendari, i relativi orari, i luoghi e le informazioni di contatto dei partecipanti. Le informazioni sulla riunione, ad esempio l'ordine del giorno, il contenuto o il titolo, non sono incluse nei dati del calendario.
* Dati relativi agli interessi: includono gli interessi associati all'account e basati sull'uso di servizi Microsoft, ad esempio Cortana e Bing per le aziende.
* Dati sulle sottoscrizioni: includono le sottoscrizioni fornite dall'organizzazione ad app e servizi Microsoft, ad esempio Office 365.
* Dati dei contatti: includono gli elenchi dei contatti di Outlook, Skype e altri servizi Microsoft, comprese le informazioni di contatto relative alle persone con le quali si collabora e si comunica frequentemente. I contatti verranno periodicamente importati, archiviati e usati da LinkedIn, ad esempio per suggerire connessioni, consentire di organizzare i contatti e visualizzare gli eventuali aggiornamenti.

I dati a cui si accede da Microsoft non vengono archiviati in modo permanente in LinkedIn, tranne i contatti. I contatti di Microsoft rimangono archiviati in LinkedIn fino a quando non vengono rimossi. Per altre informazioni, vedere la pagina sull'[eliminazione dei contatti importati da LinkedIn](https://www.linkedin.com/help/linkedin/answer/43377).

Per altre informazioni sull'uso dei dati personali da parte di LinkedIn, vedere l'[Informativa sulla privacy](https://www.linkedin.com/legal/privacy-policy) di LinkedIn. Per i servizi LinkedIn, il trasferimento dei dati e archiviazione, i dati possono scorrere dall'Unione europea per gli Stati Uniti e il back e la privacy degli utenti è protetto come descritto in [trasferimenti di dati dell'Unione europea](https://www.linkedin.com/help/linkedin/answer/62533).

## <a name="next-steps"></a>Passaggi successivi

* [LinkedIn nelle applicazioni Microsoft con l'account aziendale o dell'istituto di istruzione](https://www.linkedin.com/help/linkedin/answer/84077)
