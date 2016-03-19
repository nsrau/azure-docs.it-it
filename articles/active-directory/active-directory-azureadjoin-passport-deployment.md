<properties 
	pageTitle="Abilitare Microsoft Passport for Work nell'organizzazione | Microsoft Azure" 
	description="Istruzioni per la distribuzione per abilitare Microsoft Passport all'interno dell'organizzazione." 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/19/2015" 
	ms.author="femila"/>

# Abilitare Microsoft Passport for Work nell'organizzazione.

Dopo aver connesso i dispositivi appartenenti a un dominio di Windows 10 ad Azure AD eseguire le operazioni seguenti per abilitare Microsoft Passport for Work nell'organizzazione.

## Distribuire System Center Configuration Manager versione 1509 per la Technical Preview
Per distribuire certificati utente in base alle chiavi di Microsoft Passport, è necessario quanto segue:

- **System Center Configuration Manager versione 1509 per la Technical Preview**. Per altre informazioni, vedere l'articolo sulla [Technical Preview di Microsoft System Center Configuration Manager](https://technet.microsoft.com/library/dn965439.aspx#BKMK_TP3Update) e il [blog del team di System Center Configuration Manager](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx).
- **Infrastruttura PKI**: per abilitare Microsoft Passport for Work utilizzando i certificati utente, è necessario disporre di un'infrastruttura PKI. Se non si dispone di un'infrastruttura di questo tipo o non si desidera utilizzarla per i certificati utente, è possibile distribuire controller di dominio con la nuova versione di Windows Server:
 - **Distribuire un controller di dominio con la nuova versione di Windows server**: su una nuova installazione di Windows Server build 10551 o successiva (ISO disponibili per il download dal sito [Signiant Media Exchange](https://datatransfer.microsoft.com/signiant_media_exchange/spring/main?sdkAccessible=true)) seguire la procedura per [installare un controller di dominio replica in un dominio esistente](https://technet.microsoft.com/it-IT/library/jj574134.aspx) o per [installare una nuova foresta di Active Directory, se si sta creando un ambiente completamente nuovo](https://technet.microsoft.com/it-IT/library/jj574134.aspx).

## Configurare Microsoft Passport for Work tramite Criteri di gruppo in Active Directory

 È possibile utilizzare Criteri di gruppo di Active Directory per configurare i dispositivi appartenenti al dominio di Windows 10 per fornire credenziali utente Microsoft Passport all'accesso degli utenti a Windows:

1. 	Aprire Server Manager e passare a **Strumenti** > **Gestione Criteri di gruppo**.
2.	Da Gestione Criteri di gruppo passare al nodo corrispondente al dominio in cui si desidera abilitare la funzionalità di aggiunta ad Azure AD.
3.	Fare clic con il pulsante destro del mouse su **Oggetti Criteri di gruppo** e scegliere **Nuovo**. Assegnare un nome all'oggetto Criteri di gruppo, ad esempio **Abilitazione di Microsoft Passport**. Fare clic su **OK**.
4.	Fare clic con il pulsante destro del mouse sul nuovo oggetto Criteri di gruppo e scegliere **Modifica**.
5.	Passare a **Configurazione computer** > **Criteri** > **Modelli amministrativi** > **Componenti di Windows** > **Passport for Work**.
6.	Fare doppio clic su **Abilita Passport for Work** e quindi selezionare **Modifica**.
7.	Selezionare il pulsante di opzione **Abilitato** e fare clic su **Applica**. Fare clic su **OK**.
8.	È ora possibile collegare l'oggetto Criteri di gruppo a una posizione di propria scelta. Per abilitare questo oggetto per tutti i dispositivi Windows 10 aziendali aggiunti al dominio, collegarlo al dominio. ad esempio:
 - Una specifica unità organizzativa (OU) in AD in cui verranno posizionati i computer aggiunti al dominio di Windows 10.
 - Uno specifico gruppo di sicurezza contenente i computer appartenenti al dominio di Windows 10 che verranno registrati automaticamente con Azure AD.

## Configurare Microsoft Passport for Work mediante la distribuzione di PowerShell tramite Gestione configurazione 

Eseguire il seguente comando PowerShell:

    powershell.exe -ExecutionPolicy Bypass -NoLogo -NoProfile -Command "& {New-ItemProperty "HKLM:\Software\Policies\Microsoft\PassportForWork" -Name "Enabled" -Value 1 -PropertyType "DWord" -Force}"

## Configurare il profilo certificato per registrare il certificato di registrazione di "Passport for Work" in Gestione configurazione
Per utilizzare l'accesso basato su certificato di Passport for Work o Microsoft Hello, configurare il profilo certificato (Asset e conformità -> Impostazioni di conformità -> Company Resource Access -> Profili certificato) selezionando un modello dotato di EKU per l'accesso con smart card.

##Configurare un'attività pianificata, che si attiva quando il contenitore Passport for Work viene abilitato e richiede la valutazione del certificato
Come correzione a breve termine, gli amministratori devono creare un'attività pianificata che resta in attesa della creazione di un account Passport for Work e richiede la valutazione del certificato. In questo modo si riduce il ritardo nell'impostazione del contenitore e del PIN ed è possibile utilizzare questi ultimi all'accesso successivo.

**Per creare l'attività pianificata, utilizzare il comando seguente oppure ** schtasks /create /xml %0..<EnrollCertificate.xml> /tn <Task Name> dell'interfaccia utente

Di seguito è riportato codice xml di esempio:

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

Dove ScopeId\_73F3BB5E-5EDC-4928-87BD-4E75EB4BBC34/ConfigurationPolicy\_db89c51e-1d1b-4a17-8e42-a5459b742ccd è l'ID del profilo certificato creato nel passaggio "Configurare il profilo certificato per registrare il certificato di registrazione di Passport for Work in Gestione configurazione" e <8> è la versione.

## Informazioni aggiuntive
* [Windows 10 per le aziende: modalità d'uso dei dispositivi di lavoro](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estensione delle funzionalità del cloud ai dispositivi Windows 10 tramite Aggiunta ad Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Autenticazione delle identità senza password con Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Scenari di utilizzo per Aggiunta ad Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connettere dispositivi appartenenti a un dominio ad Azure AD per usufruire di Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurare Aggiunta di Azure AD](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_1125_2015-->