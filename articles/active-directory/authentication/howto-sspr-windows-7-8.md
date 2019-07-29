---
title: Reimpostazione della password self-service di Azure AD 8.1 - Azure Active Directory e Windows 7
description: Come abilitare la reimpostazione della password self-service usando l'opzione Password dimenticata nella schermata di accesso di Windows 7 o 8.1
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08794a7605ffbd3cd5d4b021b783f32afb190727
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65861587"
---
# <a name="how-to-enable-password-reset-from-windows-7-8-and-81"></a>Procedura: Abilitare la reimpostazione della password da Windows 7, 8 e 8.1

Mentre gli amministratori possono abilitare la funzione di reimpostazione della password self-service (SSPR), gli utenti devono contattare il supporto tecnico per reimpostare la password, poiché non possono accedere al [portale di reimpostazione della password self-service](https://aka.ms/sspr) tramite una finestra del browser. Nei computer Windows 10 è possibile abilitare il collegamento "Reimposta password" nella schermata di accesso seguendo l'esercitazione [Reimpostazione password self-service di Azure AD dalla schermata di accesso](tutorial-sspr-windows.md). Le linee guida seguenti, invece, consentono agli utenti di Windows 7, 8 e 8.1 di reimpostare la password usando la funzione di reimpostazione della password self-service nella schermata di accesso di Windows.

A differenza dei computer Windows 10, i computer Windows 7, 8 e 8.1 non hanno requisiti di appartenenza a un dominio di Azure Active Directory per la reimpostazione della password.

![Esempio di schermata di accesso di Windows 7 con l'opzione "Password dimenticata?" collegamento visualizzato](media/howto-sspr-windows-7-8/windows-7-logon-screen.png)

## <a name="prerequisites"></a>Prerequisiti

* È necessario abilitare la reimpostazione password self-service di Azure AD.
* Sistema operativo Windows 7 con patch o Windows 8.1.
* Protocollo TLS 1.2 abilitato seguendo le indicazioni riportate in [Transport Layer Security (TLS) registry settings](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12) (Impostazioni del Registro di sistema di Transport Layer Security - TLS).

> [!WARNING]
> Il protocollo TLS 1.2 deve essere abilitato, non semplicemente impostato sulla negoziazione automatica.

## <a name="install"></a>Installa

1. Scaricare il programma di installazione appropriato per la versione di Windows che si vuole abilitare.

   1. Il software è disponibile nell'Area download Microsoft all'indirizzo [https://aka.ms/sspraddin](https://aka.ms/sspraddin)

1. Accedere al computer in cui si vuole effettuare l'installazione ed eseguire il programma di installazione.
1. Al termine dell'installazione è consigliabile riavviare il computer.
1. Dopo il riavvio, nella schermata di accesso scegliere un utente e fare clic su "Password dimenticata?" per avviare il flusso di lavoro di reimpostazione della password.
1. Completare il flusso di lavoro seguendo i passaggi visualizzati sullo schermo per reimpostare la password.

![Esempio dell'opzione "Password dimenticata?" selezionata in Windows 7 Flusso SSPR](media/howto-sspr-windows-7-8/windows-7-sspr.png)

### <a name="silent-installation"></a>Installazione invisibile all'utente

* Per l'installazione invisibile all'utente, usare il comando "msiexec /i SsprWindowsLogon.PROD.msi /qn"
* Per la disinstallazione invisibile all'utente, usare il comando "msiexec /x SsprWindowsLogon.PROD.msi /qn"

## <a name="caveats"></a>Avvertenze

È necessario registrarsi per la funzione di reimpostazione della password self-service prima di poter usare il collegamento "Password dimenticata".

![Informazioni di sicurezza aggiuntive necessarie per la reimpostazione della password](media/howto-sspr-windows-7-8/windows-7-sspr-need-security-info.png)

In questa versione iniziale non è possibile usare l'app Microsoft Authenticator per le notifiche e i codici necessari per la reimpostazione della password. Gli utenti devono avere precedentemente registrato metodi alternativi conformi ai requisiti dei criteri.

Se il provider di credenziali di più di uno 3rd party è abilitato nel computer, gli utenti visualizzeranno nella schermata di accesso più di un profilo utente.

## <a name="troubleshooting"></a>risoluzione dei problemi

Gli eventi verranno registrati sia nel computer sia in Azure AD.

Gli eventi di Azure AD includeranno informazioni sull'indirizzo IP e sul tipo di client in cui si è verificata la reimpostazione della password.

![Esempio Windows 7 la reimpostazione della password nel log di controllo di Azure AD](media/howto-sspr-windows-7-8/windows-7-sspr-azure-ad-audit-log.png)

Se è necessaria una registrazione aggiuntiva, è possibile modificare una chiave del Registro di sistema per abilitare la registrazione dettagliata. Abilitare la registrazione dettagliata solo per scopi di risoluzione dei problemi.

`HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}`

* Per abilitare la registrazione dettagliata, creare un REG_DWORD: "EnableLogging" e impostarlo su 1.
* Per disabilitare la registrazione dettagliata, creare un REG_DWORD: "EnableLogging" e impostarlo su 0.

Se i computer Windows 7, 8, e 8.1 sono protetti da un server proxy o da un firewall, deve essere consentito il traffico HTTPS (443) verso passwordreset.microsoftonline.com.

## <a name="next-steps"></a>Passaggi successivi

* [Consentire agli utenti di Windows 10 di reimpostare la password nella schermata di accesso](tutorial-sspr-windows.md)
