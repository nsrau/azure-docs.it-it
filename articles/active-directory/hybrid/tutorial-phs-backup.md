---
title: 'Esercitazione:  Configurare PHS come backup per AD FS in Azure AD Connect | Microsoft Docs'
description: L'articolo illustra come attivare la sincronizzazione degli hash delle password come backup e per AD FS.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e5ad7badfa44a006fd7e71d3b0e42ee95ac698d
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64918998"
---
# <a name="tutorial--setting-up-phs-as-backup-for-ad-fs-in-azure-ad-connect"></a>Esercitazione:  Configurare PHS come backup per AD FS in Azure AD Connect

L'esercitazione seguente mostra come eseguire l'impostazione della sincronizzazione hash delle password come backup e failover per AD FS.  Questo documento spiega anche come abilitare la sincronizzazione degli hash delle password come metodo di autenticazione primaria, se AD FS non è riuscito o non è più disponibile.

>[!NOTE] 
>Anche se questi passaggi vengono in genere eseguiti durante situazioni di emergenza o di interruzione del servizio, è consigliabile testarli e verificare le procedure prima che si verifichi un'interruzione.

>[!NOTE]
>Nel caso in cui non si abbia accesso al server Azure AD Connect o che il server non abbia accesso a Internet, è possibile contattare il [supporto tecnico Microsoft](https://support.microsoft.com/en-us/contactus/) per chiedere assistenza con le modifiche da apportare sul lato Azure AD.

## <a name="prerequisites"></a>Prerequisiti
Questa esercitazione si basa sull'esercitazione [: Attuare la federazione di un singolo ambiente di foresta di Active Directory nel cloud](tutorial-federation.md) ed è un prerequisito prima di provare questa esercitazione.  Se non è stata completata questa esercitazione, eseguire questa operazione prima di provare i passaggi descritti in questo documento.

>[!IMPORTANT]
>Prima di passare a PHS, è consigliabile creare una copia di backup dell'ambiente AD FS.  A questo scopo, è possibile usare lo [strumento di ripristino rapido di AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool#how-to-use-the-tool).

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
A questo punto verrà illustrato come passare alla sincronizzazione dell'hash delle password. Prima di iniziare, considerare in quali condizioni si deve effettuare il passaggio. Non effettuare il passaggio per motivi temporanei, come un'interruzione della rete, un problema minore di AD FS o un problema che interessa un sottoinsieme di utenti. Se si decide di effettuare il passaggio perché la risoluzione del problema richiede troppo tempo, eseguire le operazioni seguenti:

> [!IMPORTANT]
> Tenere presente che la sincronizzazione degli hash delle password con Azure AD può richiedere del tempo.  Quindi possono essere necessarie fino a 3 ore prima che la sincronizzazione venga completata e che si possa iniziare ad autenticare gli utenti con gli hash delle password.

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

1. Passare a [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Accedere con uno degli account utente creati nel nuovo tenant.  Per accedere, sarà necessario usare il formato seguente: (user@domain.onmicrosoft.com). Usare la stessa password che l'utente usa per accedere in locale.</br>
   ![Verificare](media/tutorial-password-hash-sync/verify1.png)</br>

## <a name="switch-back-to-federation"></a>Tornare alla federazione
Ora verrà illustrato come tornare alla federazione.  A tale scopo, eseguire queste operazioni:

1.  Fare doppio clic sull'icona Azure AD Connect creata sul desktop
2.  Fare clic su **Configure**.
3.  Selezionare **Change user sign-in** (Cambia l'accesso utente) e quindi fare clic su **Next** (Avanti).
4.  Immettere nome utente e password per l'amministratore globale.  Questo è l'account creato [qui](tutorial-federation.md#create-a-global-administrator-in-azure-ad) nell'esercitazione precedente.
5.  Nella schermata **Accesso utente** selezionare **Federazione tramite AD FS** e fare clic su **Avanti**.  
6. Nella pagina Credenziali dell'amministratore di dominio immettere il nome utente e la password di contoso\Administrator e fare clic su **Avanti**.
7. Nella schermata Farm AD FS fare clic su **Avanti**.
8. Nella schermata **Dominio di Azure AD** selezionare il dominio nell'elenco a discesa e fare clic su **Avanti**.
9. Nella schermata **Ready to configure** (Pronto per la configurazione) fare clic su **Configure** (Configura).
10. Dopo aver completato la configurazione, fare clic su **Avanti**.
![Configurare](media/tutorial-phs-backup/backup4.png)</br>
11. Nella schermata **Verifica la connettività della federazione** fare clic su **Verifica**.  Per il corretto completamento di questa operazione, può essere necessario configurare i record DNS (aggiungere i record A e AAAA).
![Verificare](media/tutorial-phs-backup/backup5.png)</br>
12. Fare clic su **Esci**.

## <a name="reset-the-ad-fs-and-azure-trust"></a>Reimpostare la relazione di trust tra AD FS e Azure
Ora è necessario reimpostare la relazione di trust tra AD FS e Azure.

1.  Fare doppio clic sull'icona Azure AD Connect creata sul desktop
2.  Fare clic su **Configura**.
3.  Selezionare **Gestisci la federazione** e fare clic su **Avanti**.
4.  Selezionare **Reimposta il trust di Azure AD** e fare clic su **Avanti**.
![Reimpostazione](media/tutorial-phs-backup/backup6.png)</br>
5.  Nella schermata **Connessione ad Azure AD** immettere il nome utente e la password dell'amministratore globale.
6.  Nella schermata **Connetti ad AD FS** immettere il nome utente e la password di contoso\Administrator e fare clic su **Avanti**.
7.  Nella schermata **Certificati** fare clic su **Avanti**.

## <a name="test-signing-in-with-one-of-our-users"></a>Testare l'accesso con uno degli utenti

1.  Passare a [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Accedere con uno degli account utente creati nel nuovo tenant.  Per accedere, sarà necessario usare il formato seguente: (user@domain.onmicrosoft.com). Usare la stessa password che l'utente usa per accedere in locale.
![Verificare](media/tutorial-password-hash-sync/verify1.png)

La configurazione di un ambiente ibrido di gestione delle identità è stata completata. A questo punto è possibile usare questo ambiente a scopo di test o per acquisire familiarità con le funzionalità di Azure.

## <a name="next-steps"></a>Passaggi successivi


- [Hardware e prerequisiti](how-to-connect-install-prerequisites.md) 
- [Impostazioni rapide](how-to-connect-install-express.md)
- [Sincronizzazione dell'hash delle password](how-to-connect-password-hash-synchronization.md)
