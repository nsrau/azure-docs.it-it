---
title: Password escluse in modo dinamico in Azure AD
description: Escludere le password vulnerabili dall'ambiente con le password escluse in modo dinamico di Azure AD
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: rogoya
ms.openlocfilehash: 89cbe386d87c6ccb81df7fabd86b197bb69e41e1
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2018
ms.locfileid: "36291956"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Eliminare le password non appropriate nell'organizzazione

|     |
| --- |
| La protezione password e l'elenco personalizzato di password escluse di Azure AD sono funzionalità in anteprima pubblica di Azure Active Directory. Per altre informazioni sulle funzionalità in anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Leader del settore suggeriscono di non usare la stessa password in più posizioni e di usare password complesse e non semplici come Password123. In che modo le organizzazioni possono garantire che gli utenti seguano queste linee guida? Come possono assicurarsi che gli utenti non usino password comuni o password coinvolte in violazioni dei dati avvenute di recente?

## <a name="global-banned-password-list"></a>Elenco globale di password escluse

Microsoft cerca sempre di anticipare le mosse dei criminali informatici. Il team di Azure AD Identity Protection è pertanto continuamente alla ricerca di password usate comunemente e compromesse. Queste password ritenute troppo comuni vengono quindi bloccate attraverso un elenco globale di password escluse. I criminali informatici usano strategie simili negli attacchi, quindi Microsoft non rende disponibile il contenuto di questo elenco pubblicamente. Queste password vulnerabili vengono bloccate prima di diventare una minaccia reale per i clienti Microsoft. Per altre informazioni sulle attività volte a garantire la sicurezza, vedere il [Microsoft Security Intelligence Report](https://www.microsoft.com/security/intelligence-report).

## <a name="preview-custom-banned-password-list"></a>Anteprima: Elenco personalizzato di password escluse

Alcune organizzazioni potrebbero voler aumentare ulteriormente la sicurezza personalizzando l'elenco globale di password escluse tramite l'aggiunta di voci all'elenco personalizzato di password escluse Microsoft. Clienti aziendali come Contoso possono quindi scegliere di bloccare le varianti del nome dell'organizzazione, termini aziendali specifici o altre voci.

L'elenco personalizzato di password escluse e la possibilità di abilitare l'integrazione di Active Directory locale vengono gestiti tramite il portale di Azure.

![Modificare l'elenco personalizzato di password escluse nella sezione Metodi di autenticazione nel portale di Azure](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="on-premises-hybrid-scenarios"></a>Scenari ibridi locali

La protezione degli account solo cloud è utile, ma molte organizzazioni gestiscono scenari ibridi, incluso Windows Server Active Directory locale. È possibile installare gli agenti di protezione password di Azure AD per Windows Server Active Directory (anteprima) in locale per estendere gli elenchi di password escluse all'infrastruttura esistente. In questo caso, utenti e amministratori che modificano, impostano o reimpostano le password in locale devono rispettare gli stessi criteri password degli utenti solo cloud.

## <a name="how-does-the-banned-password-list-work"></a>Modalità di funzionamento dell'elenco di password escluse

L'elenco di password escluse consente di trovare la corrispondenza con le password presenti nell'elenco convertendo la stringa in lettere minuscole ed eseguendo un confronto con le password escluse note in una distanza di edit pari a 1 con corrispondenza fuzzy.

Esempio: la parola password è stata bloccata per un'organizzazione
   - Un utente cerca di impostare la password "P@ssword" che viene convertita in "p@ssword" e bloccata in quanto variante di password.
   - Un amministratore cerca di impostare una password utente su "Password123!" che viene convertita in "password123!" e bloccata in quanto variante di password.

Ogni volta che un utente reimposta o modifica la password di Azure AD, viene applicato questo processo, per verificare che la password non sia presente nell'elenco di password escluse. Questo controllo è incluso negli scenari ibridi che usano la reimpostazione della password self-service, la sincronizzazione dell'hash delle password e l'autenticazione pass-through.

## <a name="license-requirements"></a>Requisiti relativi alle licenze

I vantaggi dell'elenco globale di password escluse si applicano a tutti gli utenti di Azure Active Directory (Azure AD).

L'elenco personalizzato di password escluse richiede licenze di Azure AD Basic.

La protezione password di Azure AD per Windows Server Active Directory richiede licenze di Azure AD Premium. 

Informazioni aggiuntive sulle licenze, inclusi i costi, sono disponibili nella pagina [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-users-see"></a>Cosa vedono gli utenti

Quando un utente cerca di reimpostare una password usando una stringa che verrà esclusa, viene visualizzato il messaggio di errore seguente:

La password contiene una parola o una frase o segue uno schema che la rende facile da indovinare. Riprovare con una password diversa.

## <a name="next-steps"></a>Passaggi successivi

* [Configurare l'elenco personalizzato di password escluse](howto-password-ban-bad.md)
* [Abilitare gli agenti di protezione password di Azure AD in locale](howto-password-ban-bad-on-premises.md)
