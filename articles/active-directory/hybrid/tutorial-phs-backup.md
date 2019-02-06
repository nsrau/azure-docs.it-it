---
title: 'Esercitazione:  Configurare PHS come backup per AD FS in Azure AD Connect | Microsoft Docs'
description: L'articolo illustra come attivare la sincronizzazione degli hash delle password come backup e per AD FS.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 01/30/2019
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 7e7c9ca30659fd1e99989bb77406b653a8ed9e7f
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55302980"
---
# <a name="tutorial--setting-up-phs-as-backup-for-ad-fs-in-azure-ad-connect"></a>Esercitazione:  Configurare PHS come backup per AD FS in Azure AD Connect

L'esercitazione seguente mostra come eseguire l'impostazione della sincronizzazione hash delle password come backup e failover per AD FS.  Questo documento spiega anche come abilitare la sincronizzazione degli hash delle password come metodo di autenticazione primaria, se AD FS non è riuscito o non è più disponibile.

## <a name="prerequisites"></a>Prerequisiti
Questa esercitazione si basa sull'esercitazione [: Attuare la federazione di un singolo ambiente di foresta di Active Directory nel cloud](tutorial-federation.md) ed è un prerequisito prima di provare questa esercitazione.  Se non è stata completata questa esercitazione, eseguire questa operazione prima di provare i passaggi descritti in questo documento.

## <a name="enable-phs-in-azure-ad-connect"></a>Abilitare PHS per Azure AD Connect
Il primo passaggio, ora che esiste un ambiente di Azure AD Connect che usa la federazione, è attivare la sincronizzazione degli hash delle password e consentire ad Azure AD Connect di sincronizzare gli hash.

Eseguire le operazioni seguenti:

1.  Fare doppio clic sull'icona Azure AD Connect creata sul desktop
2.  Fare clic su **Configure**.
3.  Nella pagina Attività aggiuntive selezionare **Customize synchronization options** (Personalizzazione delle opzioni di sincronizzazione) e fare clic su **Next** (Avanti).
4.  Immettere nome utente e password per l'amministratore globale.  Questo account è stato creato [qui](tutorial-federation.md#create-a-global-administrator-in-azure-ad) nell'esercitazione precedente.
5.  Nella schermata **Connect your directories** (Connessione delle directory) fare clic su **Next** (Avanti).
6.  Nella schermata **Domain and OU filtering** (Filtro di domini e unità organizzative) fare clic su **Next** (Avanti).
7.  Nella schermata **Optional features** (Funzionalità facoltative) selezionare **Password hash synchronization** (Sincronizzazione hash password) e fare clic su **Next** (Avanti).
![Select](media/tutorial-phs-backup/backup1.png)</br>
8.  Nella schermata **Ready to configure** (Pronto per la configurazione) fare clic su **Configure** (Configura).
9.  Quando la Configurazione è completata, fare clic su **Exit** (Esci).
10. L'operazione è terminata.  La procedura è finita.  La sincronizzazione dell'hash delle password a questo punto verrà eseguita e può essere utilizzata come backup se AD FS non è più disponibile.

## <a name="switch-to-password-hash-synchronization"></a>Passare alla sincronizzazione degli hash delle password
A questo punto, se è necessario, verrà illustrato come passare alla sincronizzazione dell'hash delle password.  Eseguire le operazioni seguenti:

1. Fare doppio clic sull'icona Azure AD Connect creata sul desktop
2.  Fare clic su **Configure**.
3.  Selezionare **Change user sign-in** (Cambia l'accesso utente) e quindi fare clic su **Next** (Avanti).
![Modifica](media/tutorial-phs-backup/backup2.png)</br>
4.  Immettere nome utente e password per l'amministratore globale.  Questo account è stato creato [qui](tutorial-federation.md#create-a-global-administrator-in-azure-ad) nell'esercitazione precedente.
5.  Nella schermata **User sign-in** (Accesso dell'utente), selezionare **Password Hash Synchronization** (Sincronizzazione dell'hash delle password) e inserire un segno di spunta sulla casella **Sincronizzazione dell'hash delle password** (Non convertire gli account utente).  
6.  Lasciare l'impostazione predefinita **Enable single sign-on** (Abilita Single sign-on) selezionata e fare clic su **Next** (Avanti).
7.  Nella schermata **Enable single sign-on** (Abilita Single sign-on) fare clic su **Next** (Avanti).
8.  Nella schermata **Ready to configure** (Pronto per la configurazione) fare clic su **Configure** (Configura).
9.  Dopo aver completato la configurazione, fare clic su **Exit** (Esci).
10. Gli utenti possono ora usare le password per accedere ad Azure e Servizi di Azure.

## <a name="test-signing-in-with-one-of-our-users"></a>Testare l'accesso con uno degli utenti

1.  Passare a [http://myapps.microsoft.com](http://myapps.microsoft.com)
2. Accedere con uno degli account utente creati nel nuovo tenant.  Per accedere, sarà necessario usare il formato seguente: (user@domain.onmicrosoft.com). Usare la stessa password che l'utente usa per accedere in locale.</br>
![Verificare](media/tutorial-password-hash-sync/verify1.png)</br>

## <a name="next-steps"></a>Passaggi successivi


- [Hardware e prerequisiti](how-to-connect-install-prerequisites.md) 
- [Impostazioni rapide](how-to-connect-install-express.md)
- [Sincronizzazione degli hash delle password](how-to-connect-password-hash-synchronization.md)|
