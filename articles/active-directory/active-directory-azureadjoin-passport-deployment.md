<properties
	pageTitle="Abilitare Microsoft Windows Hello for Business nell'organizzazione | Microsoft Azure"
	description="Istruzioni per la distribuzione per abilitare Microsoft Passport all'interno dell'organizzazione."
	services="active-directory"
	documentationCenter=""
	keywords="configurare Microsoft Passport, distribuzione di Microsoft Windows Hello for Business"
	authors="markusvi"
	manager="femila"
	editor=""
	tags="azure-classic-portal"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/28/2016"
	ms.author="femila;markvi"/>


# Abilitare Microsoft Windows Hello for Business nell'organizzazione

Dopo aver connesso i dispositivi appartenenti a un dominio di Windows 10 ad Azure Active Directory (Azure AD), seguire questa procedura per abilitare Microsoft Windows Hello for Business nell'organizzazione.

## Distribuire System Center Configuration Manager Current Branch 
Per distribuire certificati utente in base alle chiavi di Windows Hello for Business, è necessario quanto segue:

- **System Center Configuration Manager Current Branch**. È necessario installare la versione 1606 o una versione successiva. Per altre informazioni, vedere la [documentazione di System Center Configuration Manager](https://technet.microsoft.com/library/mt346023.aspx) e il [blog del team di System Center Configuration Manager](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx).
- **Infrastruttura a chiave pubblica (PKI)**: per abilitare Microsoft Windows Hello for Business usando i certificati utente, è necessario avere un'infrastruttura PKI. Se l'infrastruttura non è disponibile o l'utente non vuole usarla per i certificati utente, è possibile seguire questa procedura:
 - **Distribuire un controller di dominio**: distribuire un nuovo controller di dominio con Windows Server 2016 build 10551 (o versione successiva) installato e seguire i passaggi per [installare un controller di dominio di replica in un dominio esistente](https://technet.microsoft.com/library/jj574134.aspx) o per [installare una nuova foresta di Active Directory, se si crea un nuovo ambiente](https://technet.microsoft.com/library/jj574166). I file con estensione iso sono disponibili per il download in [Signiant Media Exchange](https://datatransfer.microsoft.com/signiant_media_exchange/spring/main?sdkAccessible=true).

## Configurare Microsoft Windows Hello for Business tramite Criteri di gruppo in Active Directory

 È possibile usare Criteri di gruppo in Windows Server Active Directory per configurare i dispositivi appartenenti a un dominio di Windows 10 in modo da effettuare il provisioning delle credenziali utente di Windows Hello for Business all'accesso degli utenti a Windows:

1. 	Aprire Server Manager e passare a **Strumenti** > **Gestione Criteri di gruppo**.
2.	Da Gestione Criteri di gruppo passare al nodo corrispondente al dominio in cui abilitare la funzionalità di aggiunta ad Azure AD.
3.	Fare clic con il pulsante destro del mouse su **Oggetti Criteri di gruppo** e scegliere **Nuovo**. Assegnare un nome all'oggetto Criteri di gruppo, ad esempio Abilita Windows Hello for Business. Fare clic su **OK**.
4.	Fare clic con il pulsante destro del mouse sul nuovo oggetto Criteri di gruppo e scegliere **Modifica**.
5.	Passare a **Configurazione computer** > **Criteri** > **Modelli amministrativi** > **Componenti di Windows** > **Windows Hello for Business**.
6.	Fare doppio clic su **Abilita Windows Hello for Business** e selezionare **Modifica**.
7.	Selezionare il pulsante di opzione **Abilitato** e fare clic su **Applica**. Fare clic su **OK**.
8.	È ora possibile collegare l'oggetto Criteri di gruppo a una posizione di propria scelta. Per abilitare questo oggetto per tutti i dispositivi appartenenti a un dominio di Windows 10 nell'organizzazione, collegarlo al dominio. ad esempio:
 - Una specifica unità organizzativa in Active Directory in cui saranno posizionati i computer Windows 10 aggiunti a un dominio.
 - Uno specifico gruppo di sicurezza contenente i computer appartenenti a un dominio di Windows 10 che verranno registrati automaticamente in Azure AD.


## Configurare Windows Hello for Business mediante System Center Configuration Manager

È possibile usare System Center Configuration Manager solo per distribuire i criteri di Windows Hello for Business per Windows 10 versione 1607. Se è necessario cercare la versione del computer Windows 10, avviare winver nel computer Windows 10.

1. Aprire **System Center Configuration Manager**, passare ad **Asset e conformità > Impostazioni di conformità > Accesso risorse aziendali > Profili Windows Hello for Business**.

	![Configurare Windows Hello for Business](./media/active-directory-azureadjoin-passport-deployment/01.png)


2. Nella barra degli strumenti nella parte superiore fare clic su **Crea profilo Windows Hello for Business**.

	![Configurare Windows Hello for Business](./media/active-directory-azureadjoin-passport-deployment/02.png)

2. Nella finestra di dialogo **Generale** seguire questa procedura:

	![Configurare Windows Hello for Business](./media/active-directory-azureadjoin-passport-deployment/03.png)

	a. Nella casella di testo **Nome** digitare un nome per il profilo, ad esempio **Il mio profilo WHfB**.

	b. Fare clic su **Next**.


2. Nella finestra di dialogo **Piattaforme supportate** selezionare le piattaforme che saranno fornite con questo profilo Windows Hello for Business e fare clic su **Avanti**.

	![Configurare Windows Hello for Business](./media/active-directory-azureadjoin-passport-deployment/04.png)


2. Nella finestra di dialogo **Impostazioni** seguire questa procedura:

	![Configurare Windows Hello for Business](./media/active-directory-azureadjoin-passport-deployment/05.png)

	a. Per **Configura Windows Hello for Business** selezionare **Attivato**.

	b. Per **Usa Trusted Platform Module (TPM)** selezionare **Richiesto**.

	c. Per **Metodo di autenticazione** selezionare Basata su certificati**.

	d. Fare clic su **Next**.



2. Nella finestra di dialogo **Riepilogo** fare clic su **Avanti**.

2. Nella finestra di dialogo **Completamento** fare clic su **Chiudi**.


2. Nel barra degli strumenti in alto fare clic su **Distribuisci**.

	![Configurare Windows Hello for Business](./media/active-directory-azureadjoin-passport-deployment/06.png)



## Configurare il profilo certificato in modo da usare il certificato di registrazione di Windows Hello for Business in Gestione configurazione


Se si usa l'autenticazione basata su certificato per l'autenticazione locale, è necessario configurare e distribuire un profilo del certificato. Questa attività richiede di configurare un server NDES e il ruolo del sito del punto di registrazione certificati in System Center Configuration Manager. Per altre informazioni, vedere [Prerequisiti per i profili certificato in Configuration Manager](https://technet.microsoft.com/library/dn261205.aspx).

1. Aprire **System Center Configuration Manager**, passare ad **Asset e conformità > Impostazioni di conformità > Accesso risorse aziendali > Profili certificato**.


2. Selezionare un modello dotato di utilizzo chiavi avanzato (EKU) per l'accesso con smart card.

Nella pagina **Registrazione SCEP** del profilo certificato è necessario scegliere **Installa in Passport for Work oppure genera errore** come **Provider di archiviazione chiavi**.



## Passaggi successivi
* [Windows 10 per le aziende: modalità d'uso dei dispositivi di lavoro](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estensione delle funzionalità del cloud ai dispositivi Windows 10 tramite Aggiunta ad Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Autenticazione delle identità senza password con Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Scenari di utilizzo per Aggiunta ad Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connettere dispositivi appartenenti a un dominio ad Azure AD per usufruire di Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurare Aggiunta di Azure AD](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0928_2016-->