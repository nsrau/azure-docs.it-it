---
title: Iscrizione self-service per le identità esterne - Azure AD
description: Informazioni su come consentire agli utenti esterni di iscriversi alle applicazioni abilitando l'iscrizione self-service. Creare un'esperienza di iscrizione dedicata personalizzando il flusso utente di iscrizione self-service.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e0325b43b6726f04d5994b60404f218ac58122d
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594830"
---
# <a name="self-service-sign-up-preview"></a>Iscrizione self-service (anteprima)
|     |
| --- |
| L'iscrizione self-service è una funzionalità in anteprima pubblica di Azure Active Directory. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Durante la condivisione di applicazioni con utenti esterni, è possibile che non si sappia sempre in anticipo chi dovrà accedere a un'applicazione. In alternativa all'invio di inviti direttamente a singoli utenti, è possibile consentire agli utenti esterni di iscriversi in autonomia ad applicazioni specifiche abilitando l'iscrizione self-service. È possibile creare un'esperienza di iscrizione dedicata personalizzando il flusso utente di iscrizione self-service. È ad esempio possibile fornire opzioni per Azure AD o i provider di identità basati su social network e raccogliere informazioni sull'utente.

## <a name="user-flow-for-self-service-sign-up"></a>Flusso utente per l'iscrizione self-service

Un flusso utente di iscrizione self-service crea un'esperienza di iscrizione per gli utenti esterni tramite l'applicazione che si vuole condividere. Il flusso utente può essere associato a una o più applicazioni. In primo luogo sarà necessario abilitare l'iscrizione self-service per il tenant e configurare la federazione con tutti i provider di identità che gli utenti esterni saranno autorizzati a usare per l'accesso. Successivamente, verrà creato e personalizzato il flusso utente di iscrizione e verranno assegnate le applicazioni al flusso.
È possibile configurare le impostazioni del flusso utente per controllare il modo in cui l'utente si iscrive all'applicazione:

- Tipi di account usati per l'accesso, ad esempio account di social network, come Facebook o account Azure AD
- Attributi da raccogliere dall'utente che si iscrive, ad esempio nome, codice postale o paese di residenza

Quando un utente vuole accedere all'applicazione, sia che si tratti di un'applicazione Web, per dispositivi mobili, desktop o a pagina singola, l'applicazione avvia una richiesta di autorizzazione a un endpoint fornito da un flusso utente. Il flusso utente definisce e controlla l'esperienza dell'utente. Al termine di un flusso utente di iscrizione, Azure AD genera un token, quindi reindirizza l'utente all'applicazione. Più applicazioni possono usare lo stesso flusso utente.

## <a name="example-of-self-service-sign-up"></a>Esempio di iscrizione self-service

L'esempio seguente illustra il modo in cui i provider di identità basati su social network vengono associati ad Azure AD con funzionalità di iscrizione self-service per gli utenti guest.  
Un partner di Woodgrove apre l'app Woodgrove. Decide di procedere all'iscrizione per ottenere un account fornitore, quindi seleziona l'opzione per richiedere l'account fornitore, che avvia il flusso di iscrizione self-service.

![Esempio di pagina iniziale dell'iscrizione self-service](media/self-service-sign-up-overview/example-start-sign-up-flow.png)

Per l'iscrizione usa l'indirizzo di posta elettronica preferito.

![Esempio che mostra la selezione di Facebook per l'accesso](media/self-service-sign-up-overview/example-sign-in-with-facebook.png)

Azure AD crea una relazione con Woodgrove usando l'account Facebook del partner e crea un nuovo account.

Woodgrove desidera ottenere ulteriori informazioni sull'utente, ad esempio il nome, il nome dell'azienda, il codice fiscale aziendale, il numero di telefono.

![Esempio che mostra gli attributi di iscrizione utente](media/self-service-sign-up-overview/example-enter-user-attributes.png)

L'utente immette le informazioni, procede con il flusso di iscrizione e ottiene l'accesso alle risorse necessarie.

![Esempio che mostra che l'utente ha eseguito l'accesso](media/self-service-sign-up-overview/example-signed-in.png)

## <a name="next-steps"></a>Passaggi successivi

 Per informazioni dettagliate, vedere come [Aggiungere l'iscrizione self-service a un'app](self-service-sign-up-user-flow.md).