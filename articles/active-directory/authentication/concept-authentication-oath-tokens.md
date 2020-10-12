---
title: Metodo di autenticazione per i token di GIURAmento-Azure Active Directory
description: Per informazioni sull'uso di token di GIURAmento, vedere Azure Active Directory per contribuire a migliorare e proteggere gli eventi di accesso
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: c62b531b0d189ad698a930d65506fff0ebee3a69
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90532664"
---
# <a name="authentication-methods-in-azure-active-directory---oath-tokens"></a>Metodi di autenticazione nei token di Azure Active Directory

OATH TOTP (Time-Based One-Time Passwords) è uno standard aperto che specifica come vengono generati i codici per password monouso (OTP). OATH TOTP può essere implementato usando un software o un hardware per generare i codici. Azure AD non supporta OATH HOTP, uno standard di generazione del codice diverso.

## <a name="oath-software-tokens"></a>Token software OATH

I token software OATH sono in genere applicazioni quali l'app Microsoft Authenticator e altre app di autenticazione. Azure AD genera la chiave privata, o il seme, che rappresenta l'input nell'app ed è usato per generare ciascun OTP.

L'app Authenticator genera automaticamente i codici quando è configurata per generare notifiche push, in modo che un utente abbia un backup anche se il dispositivo non è connesso. Possono essere usate anche applicazioni di terze parti che usano OATH TOTP per generare i codici.

Alcuni token hardware OATH TOTP sono programmabili, ovvero non contengono una chiave privata o un seme pre-programmato. Questi token hardware programmabili possono essere configurati usando la chiave privata o il seme ottenuto dal flusso di configurazione del token software. I clienti possono acquistare i token dal fornitore di propria scelta e usare la chiave privata o il seme nel processo di configurazione del fornitore.

## <a name="oath-hardware-tokens-preview"></a>Token hardware OATH (anteprima)

Azure AD supporta l'uso di token SHA-1 OATH-TOTP che aggiornano i codici ogni 30 o 60 secondi. I clienti possono acquistare questi token dal fornitore preferito.

I token hardware OATH TOTP sono in genere dotati di una chiave privata o di un seme pre-programmato nel token. Queste chiavi devono essere inserite in Azure AD come descritto nella procedura seguente. Le chiavi private sono limitate a 128 caratteri e ciò potrebbe non essere compatibile con tutti i token. La chiave privata può contenere solo i caratteri *a-z* o *A-Z* e i numeri *1-7* e deve essere codificata in *Base32*.

I token hardware OATH TOTP programmabili di cui è possibile generare un nuovo seme possono anche essere configurati con Azure AD nel flusso di configurazione del token software.

I token hardware OATH sono supportati come parte di un'anteprima pubblica. Per altre informazioni sulle funzioni in anteprima, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

![Pannello di caricamento dei token OATH nei token OATH di MFA](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

Dopo aver acquisito i token, è necessario caricarli in un formato di file con valori delimitati da virgole (CSV) includendo l'UPN, il numero di serie, la chiave privata, l'intervallo di tempo, il produttore e il modello come illustrato nell'esempio seguente:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Assicurarsi di includere la riga di intestazione nel file CSV.

Dopo aver formattato correttamente il file CSV, un amministratore può accedere al portale di Azure e passare ad **Azure Active Directory > Sicurezza > MFA > Token OATH** e caricare il file CSV risultante.

A seconda delle dimensioni del file CSV, potrebbero essere richiesti alcuni minuti per l'elaborazione. Per ottenere lo stato corrente, selezionare il pulsante **Aggiorna**. Se sono presenti errori nel file, è possibile scaricare un file CSV che elenca gli eventuali errori da risolvere. I nomi dei campi nel file CSV scaricato sono diversi da quelli della versione caricata.

Dopo aver risolto eventuali errori, l'amministratore può quindi attivare ogni chiave selezionando **Attiva** per il token da attivare e immettendo l'OTP visualizzato nel token.

Gli utenti possono avere una combinazione composta da fino a cinque token hardware OATH o applicazioni di autenticazione, quali l'app Microsoft Authenticator, configurate per l'uso in qualsiasi momento.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla configurazione dei metodi di autenticazione con l' [API REST di Microsoft Graph beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).
