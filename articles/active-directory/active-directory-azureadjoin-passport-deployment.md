<properties
	pageTitle="Abilitare Microsoft Passport for Work nell'organizzazione | Microsoft Azure"
	description="Istruzioni per la distribuzione per abilitare Microsoft Passport all'interno dell'organizzazione."
	services="active-directory"
	documentationCenter=""
	keywords="Configurare la distribuzione di Microsoft Passport, Microsoft Passport for Work"
	authors="femila"
	manager="swadhwa"
	editor=""
	tags="azure-classic-portal"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/23/2016"
	ms.author="femila"/>


# Abilitare Microsoft Passport for Work nell'organizzazione

Dopo aver connesso i dispositivi appartenenti a un dominio di Windows 10 ad Azure Active Directory (Azure AD), seguire questa procedura per abilitare Microsoft Passport for Work nell'organizzazione.

## Distribuire System Center Configuration Manager versione 1509 per la Technical Preview
Per distribuire certificati utente in base alle chiavi di Microsoft Passport, è necessario quanto segue:

- **System Center Configuration Manager versione 1509 per la Technical Preview**. Per altre informazioni, vedere [Technical Preview di Microsoft System Center Configuration Manager](https://technet.microsoft.com/library/dn965439.aspx#BKMK_TP3Update) e [Blog del team di System Center Configuration Manager](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx).
- **Infrastruttura a chiave pubblica (PKI)**: per abilitare Microsoft Passport for Work usando i certificati utente, è necessario avere un'infrastruttura PKI. Se l'infrastruttura non è disponibile o l'utente non vuole usarla per i certificati utente, è possibile seguire questa procedura:
 - **Distribuire un controller di dominio**: distribuire un nuovo controller di dominio con Windows Server 2016 build 10551 (o versione successiva) installato e seguire i passaggi per [installare un controller di dominio di replica in un dominio esistente](https://technet.microsoft.com/library/jj574134.aspx) o per [installare una nuova foresta di Active Directory, se si crea un nuovo ambiente](https://technet.microsoft.com/library/jj574166). I file con estensione iso sono disponibili per il download in [Signiant Media Exchange](https://datatransfer.microsoft.com/signiant_media_exchange/spring/main?sdkAccessible=true).

## Configurare Microsoft Passport for Work tramite Criteri di gruppo in Active Directory

 È possibile usare Criteri di gruppo in Windows Server Active Directory per configurare i dispositivi appartenenti a un dominio di Windows 10 in modo da effettuare il provisioning delle credenziali utente di Microsoft Passport all'accesso degli utenti a Windows:

1. 	Aprire Server Manager e passare a **Strumenti** > **Gestione Criteri di gruppo**.
2.	Da Gestione Criteri di gruppo passare al nodo corrispondente al dominio in cui abilitare la funzionalità di aggiunta ad Azure AD.
3.	Fare clic con il pulsante destro del mouse su **Oggetti Criteri di gruppo** e scegliere **Nuovo**. Assegnare un nome all'oggetto Criteri di gruppo, ad esempio Abilita Microsoft Passport. Fare clic su **OK**.
4.	Fare clic con il pulsante destro del mouse sul nuovo oggetto Criteri di gruppo e scegliere **Modifica**.
5.	Passare a **Configurazione computer** > **Criteri** > **Modelli amministrativi** > **Componenti di Windows** > **Passport for Work**.
6.	Fare clic con il pulsante destro del mouse su **Abilita Passport for Work** e quindi selezionare **Modifica**.
7.	Selezionare il pulsante di opzione **Abilitato** e fare clic su **Applica**. Fare clic su **OK**.
8.	È ora possibile collegare l'oggetto Criteri di gruppo a una posizione di propria scelta. Per abilitare questo oggetto per tutti i dispositivi appartenenti a un dominio di Windows 10 nell'organizzazione, collegarlo al dominio. Ad esempio:
 - Una specifica unità organizzativa in Active Directory in cui saranno posizionati i computer Windows 10 aggiunti a un dominio.
 - Uno specifico gruppo di sicurezza contenente i computer appartenenti a un dominio di Windows 10 che verranno registrati automaticamente in Azure AD.

## Configurare Microsoft Passport for Work mediante PowerShell tramite Gestione configurazione

Eseguire il seguente comando PowerShell:

    powershell.exe -ExecutionPolicy Bypass -NoLogo -NoProfile -Command "& {New-ItemProperty "HKLM:\Software\Policies\Microsoft\PassportForWork" -Name "Enabled" -Value 1 -PropertyType "DWord" -Force}"

## Configurare il profilo certificato in modo da usare il certificato di registrazione di "Passport for Work" in Gestione configurazione
Per usare l'accesso basato su certificato di Passport for Work o Microsoft Hello, configurare il profilo certificato in base a questa procedura **Asset e conformità** > **Impostazioni di conformità** > **Accesso risorse aziendali** > **Profili certificato**. Selezionare un modello dotato di utilizzo chiavi avanzato (EKU) per l'accesso con smart card.

## Configurare un'attività pianificata per richiedere la valutazione del certificato
Tale attività è una correzione a breve termine. Gli amministratori devono creare un'attività pianificata che resta in attesa della creazione di un contenitore Passport for Work e quindi richiede la valutazione del certificato. L'attività pianificata viene attivata quando il contenitore Passport for Work è abilitato. L'attività consente di ridurre il ritardo nell'impostazione del contenitore e del PIN, nonché la relativa disponibilità per l'uso all'accesso successivo.

**Per creare l'attività pianificata, è possibile usare l'interfaccia utente o il comando seguente:**

    schtasks /create /xml %0\..<EnrollCertificate.xml> /tn <Task Name>

Di seguito è illustrato l'XML di esempio:

    <?xml version="1.0" encoding="UTF-16"?>
    <Task version="1.2" xmlns="http://schemas.microsoft.com/windows/2004/02/mit/task">
       <RegistrationInfo>
         <Date>2015-09-04T17:48:45.9973761</Date>
          <Author><DomainName/UserName></Author>
        <URI>\Enroll Certificates</URI>
      </RegistrationInfo>
      <Triggers>
        <EventTrigger>
          <Enabled>true</Enabled>
          <Subscription>&lt;QueryList&gt;&lt;Query Id="0" Path="Microsoft-Windows-User Device Registration/Admin"&gt;&lt;Select Path="Microsoft-Windows-User Device Registration/Admin"&gt;*[System[Provider[@Name='Microsoft-Windows-User Device Registration'] and EventID=300]]&lt;/Select&gt;&lt;/Query&gt;&lt;/QueryList&gt;</Subscription>
        </EventTrigger>
      </Triggers>
      <Principals>
      </Principals>
      <Settings>
        <MultipleInstancesPolicy>IgnoreNew</MultipleInstancesPolicy>
        <DisallowStartIfOnBatteries>false</DisallowStartIfOnBatteries>
        <StopIfGoingOnBatteries>true</StopIfGoingOnBatteries>
        <AllowHardTerminate>true</AllowHardTerminate>
        <StartWhenAvailable>true</StartWhenAvailable>
        <RunOnlyIfNetworkAvailable>false</RunOnlyIfNetworkAvailable>
        <IdleSettings>
          <StopOnIdleEnd>true</StopOnIdleEnd>
          <RestartOnIdle>false</RestartOnIdle>
        </IdleSettings>
        <AllowStartOnDemand>true</AllowStartOnDemand>
        <Enabled>true</Enabled>
        <Hidden>false</Hidden>
        <RunOnlyIfIdle>false</RunOnlyIfIdle>
        <WakeToRun>false</WakeToRun>
        <ExecutionTimeLimit>PT1H</ExecutionTimeLimit>
        <Priority>7</Priority>
        <RestartOnFailure>
          <Interval>PT1M</Interval>
          <Count>3</Count>
        </RestartOnFailure>
      </Settings>
      <Actions Context="Author">
        <Exec>
          <Command>wmic</Command>
          <Arguments>/namespace:\\root\ccm\dcm path SMS_DesiredConfiguration call TriggerEvaluation 1,0,"ScopeId_73F3BB5E-5EDC-4928-87BD-4E75EB4BBC34/ConfigurationPolicy_db89c51e-1d1b-4a17-8e42-a5459b742ccd",8</Arguments>
        </Exec>
      </Actions>
    </Task>

 ScopeId\_73F3BB5E-5EDC-4928-87BD-4E75EB4BBC34/ConfigurationPolicy\_db89c51e-1d1b-4a17-8e42-a5459b742ccd è l'ID del profilo certificato creato nel passaggio "Configurare il profilo certificato in modo da usare il certificato di registrazione di "Passport for Work" in Gestione configurazione". Dopo l'ID ambito è presente la versione 8.

## Informazioni aggiuntive
* [Windows 10 per le aziende: modalità d'uso dei dispositivi di lavoro](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estensione delle funzionalità del cloud ai dispositivi Windows 10 tramite Aggiunta ad Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Autenticazione delle identità senza password con Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Scenari di utilizzo per Aggiunta ad Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connettere dispositivi appartenenti a un dominio ad Azure AD per usufruire di Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurare Aggiunta di Azure AD](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0629_2016-->