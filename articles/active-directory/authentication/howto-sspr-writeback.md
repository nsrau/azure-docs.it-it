---
title: Configure password writeback for SSPR - Azure Active Directory
description: Usare Azure AD e Azure AD Connect per eseguire il writeback delle password in una directory locale
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1acda877ecadc8ad0abd09b78d5453743e2470b1
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381147"
---
# <a name="how-to-configure-password-writeback"></a>Procedura: Configurare il writeback delle password

La procedura seguente presuppone che Azure AD Connect sia già stato configurato nell'ambiente tramite le impostazioni [Rapida](../hybrid/how-to-connect-install-express.md) o [Personalizzata](../hybrid/how-to-connect-install-custom.md).

1. Per configurare e abilitare il writeback delle password accedere al server Azure AD Connect e avviare la configurazione guidata di **Azure AD Connect**.
2. Nella pagina di **benvenuto** selezionare **Configura**.
3. Nella pagina **Attività aggiuntive** selezionare **Personalizzazione delle opzioni di sincronizzazione** e fare clic su **Avanti**.
4. Nella pagina **Connessione ad Azure AD** immettere le credenziali di amministratore globale e selezionare **Avanti**.
5. Nelle pagine di filtro **Connessione delle directory** e **Dominio/unità organizzativa** selezionare **Avanti**.
6. Nella pagina **Funzionalità facoltative** selezionare la casella accanto a **Writeback password** e selezionare **Avanti**.
   ![Abilitare il writeback delle password in Azure AD Connect][Writeback]
7. Nella pagina **Pronto per la configurazione** fare clic su **Configura** e attendere il completamento del processo.
8. Quando la configurazione termina, selezionare **Esci**.

Per le attività di risoluzione dei problemi comuni correlate al writeback delle password, vedere la sezione [Risolvere i problemi relativi al writeback delle password](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) nell'articolo sulla risoluzione dei problemi.

> [!WARNING]
> Per i clienti che usano Azure AD Connect 1.0.8641.0 e versioni precedenti, il writeback delle password non funzionerà più dopo il [ritiro del Servizio di controllo di accesso di Azure in data 7 novembre 2018](../develop/active-directory-acs-migration.md). A partire da tale data, Azure AD Connect 1.0.8641.0 e versioni precedenti non consentiranno più il writeback delle password perché per questa funzionalità dipendono dal Servizio di controllo di accesso di Azure.
>
> Per evitare un'interruzione del servizio, eseguire l'aggiornamento da una versione precedente di Azure AD Connect a una più recente. Vedere l'articolo [Azure AD Connect: Eseguire l'aggiornamento da una versione precedente alla versione più recente](../hybrid/how-to-upgrade-previous-version.md).
>

## <a name="licensing-requirements-for-password-writeback"></a>Requisiti di licenza per il writeback delle password

**La reimpostazione, la modifica e lo sblocco self-service della password con writeback locale sono funzionalità Premium di Azure AD**. Per altre informazioni sulle licenze, vedere il sito [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

Per usare il writeback delle password, è necessario disporre una delle licenze seguenti assegnate nel tenant:

* Azure AD P1 Premium
* Azure AD P2 Premium
* Enterprise Mobility + Security E3 o A3
* Enterprise Mobility + Security E5 o A5
* Microsoft 365 E3 o A3
* Microsoft 365 E5 o A5
* Microsoft 365 F1
* Microsoft 365 Business

> [!WARNING]
> I piani di licenza Office 365 autonomi *non supportano "Reimpostazione/modifica/sblocco con writeback in locale delle password in modalità self-service"* e richiedono uno dei piani precedenti per l'uso della funzionalità.
>

## <a name="active-directory-permissions-and-on-premises-password-complexity-policies"></a>Active Directory permissions and on-premises password complexity policies 

L'account specificato nell'utilità di Azure AD Connect deve avere i seguenti elementi impostati se si vuole essere nell'ambito per SSPR:

* **Reimpostazione della password** 
* **Cambia password** 
* **Autorizzazioni di scrittura** su `lockoutTime`
* **Autorizzazioni di scrittura** su `pwdLastSet`
* **Diritti estesi** su uno fra:
   * L'oggetto radice di *ogni dominio* in tale foresta
   * Le unità organizzative (OU) utente che si vuole siano nell'ambito per SSPR

Se non si è certi di quale sia l'account a cui l'account descritto fa riferimento, aprire l'interfaccia utente della configurazione di Azure Active Directory Connect e selezionare l'opzione **Visualizza configurazione corrente**. L'account a cui è necessario aggiungere le autorizzazioni è elencato in **Directory sincronizzate**.

Se si impostano queste autorizzazioni, l'account del servizio MA per ogni foresta può gestire le password per conto di account utente all'interno di tale foresta. 

> [!IMPORTANT]
> Se non si assegnano tali autorizzazioni, anche se il writeback è configurato correttamente, gli utenti visualizzeranno errori durante il tentativo di gestione delle password locali dal cloud.
>

> [!NOTE]
> La replica delle autorizzazioni in tutti gli oggetti nella directory potrebbe richiedere fino a un'ora o anche più tempo.
>

Per impostare le autorizzazioni appropriate per l'esecuzione del writeback delle password, eseguire la procedura seguente:

1. Aprire Utenti e computer di Active Directory con un account con le autorizzazioni appropriate per l'amministrazione del dominio.
2. Nel menu **Visualizza** verificare che l'opzione **Funzionalità avanzate** sia attivata.
3. Nel riquadro sinistro fare clic con il pulsante destro del mouse sull'oggetto che rappresenta la radice del dominio e selezionare **Proprietà** > **Sicurezza** > **Avanzate**.
4. Nella scheda **Autorizzazioni** selezionare **Aggiungi**.
5. Selezionare l'account a cui applicare le autorizzazioni, dalla configurazione di Azure AD Connect.
6. Nell'elenco a discesa **Applica a** selezionare gli oggetti **Utente discendente**.
7. In **Autorizzazioni** selezionare le caselle per le opzioni seguenti:
    * **Cambia password**
    * **Reimpostazione della password**
8. In **Proprietà** selezionare le caselle per le opzioni seguenti:
    * **Scrittura di lockoutTime**
    * **Scrittura di pwdLastSet**
9. Selezionare **Applica/OK** per applicare le modifiche e chiudere le finestre di dialogo aperte.

Since the source of authority is on premises, the password complexity policies apply from the same connected data source. Make sure you've changed the existing group policies for "Minimum password age". The group policy shouldn't be set to 1, which means password should be at least a day old before it can be updated. You need make sure it's set to 0. These settings can be found in `gpmc.msc` under **Computer Configuration > Policies > Windows Settings > Security Settings > Account Policies**. Run `gpupdate /force` to ensure that the change takes effect. 

## <a name="next-steps"></a>Passaggi successivi

[Che cos'è il writeback delle password?](concept-sspr-writeback.md)

[Writeback]: ./media/howto-sspr-writeback/enablepasswordwriteback.png "Abilitare il writeback delle password in Azure AD Connect"
