<properties 
   pageTitle="Modificare le impostazioni dei DATI 0 in un dispositivo StorSimple | Microsoft Azure"
   description="Informazioni su come utilizzare Windows PowerShell per StorSimple per riconfigurare l'interfaccia di rete DATI 0 sul dispositivo StorSimple"
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/15/2016"
   ms.author="alkohli" />

# Modificare le impostazioni dell'interfaccia di rete DATI 0 sul dispositivo StorSimple

## Panoramica

Il dispositivo Microsoft Azure StorSimple ha sei interfacce di rete, da DATI 0 a DATI 5. L'interfaccia DATI 0 è sempre configurata tramite l'interfaccia di Windows PowerShell o la console seriale ed è automaticamente abilitata per il cloud. L'interfaccia DATI 0 viene inizialmente configurata tramite l'installazione guidata durante la distribuzione iniziale del dispositivo StorSimple. Quando il dispositivo è in modalità operativa, potrebbe essere necessario riconfigurare le impostazioni di DATI 0. In questa esercitazione sono disponibili due metodi per modificare le impostazioni di rete di DATI 0, entrambi tramite Windows PowerShell per StorSimple.

Dopo aver letto questa esercitazione, si sarà in grado di:

- Modificare l'impostazione di rete di DATI 0 tramite la configurazione guidata
- Modificare le impostazioni di rete di DATI 0 tramite il cmdlet `Set-HcsNetInterface`


## Modificare le impostazioni di rete di DATI 0 tramite la configurazione guidata
È possibile riconfigurare le impostazioni di rete di DATI 0 connettendosi all'interfaccia di Windows PowerShell del dispositivo StorSimple e avviando una sessione di configurazione guidata. Eseguire i passaggi seguenti per modificare le impostazioni di DATI 0:

#### Per modificare le impostazioni di rete di DATI 0 tramite la configurazione guidata

1. Nel menu della console seriale, scegliere l'opzione 1, **Accedi con accesso completo**. Quando richiesto, fornire la **password di amministratore del dispositivo**. La password predefinita è `Password1`.

2. Al prompt dei comandi digitare:

	`Invoke-HcsSetupWizard`

3. Verrà visualizzata una procedura guidata per configurare l'interfaccia DATI 0 del dispositivo. Fornire nuovi valori per l'indirizzo IP, il gateway e la netmask.

> [AZURE.NOTE]Gli IP fissi dei controller dovranno essere riconfigurati tramite la pagina **Configura** del dispositivo StorSimple nel portale di Azure classico. Per ulteriori informazioni, andare a [Modificare le interfacce di rete](storsimple-modify-device-config.md#modify-network-interfaces).


## Modificare le impostazioni di rete di DATI 0 tramite il cmdlet Set-HcsNetInterface
Un modo alternativo per riconfigurare l'interfaccia di rete DATI 0 consiste nell'utilizzo del cmdlet `Set-HcsNetInterface`. Il cmdlet viene eseguito dall'interfaccia di Windows PowerShell del dispositivo StorSimple. Eseguire i passaggi seguenti per modificare le impostazioni di DATI 0:

#### Per modificare le impostazioni di rete di DATI 0 tramite il cmdlet Set-HcsNetInterface

1. Nel menu della console seriale, scegliere l'opzione 1, **Accedi con accesso completo**. Quando richiesto, fornire la password di amministratore del dispositivo. La password predefinita è `Password1`.

2. Al prompt dei comandi digitare:

	`Set-HCSNetInterface -InterfaceAlias Data0 -IPv4Address <> -IPv4Netmask <> -IPv4Gateway <> -Controller0IPv4Address <> -Controller1IPv4Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`
	
    Tra parentesi acute digitare i seguenti valori per DATA 0:
											
	- Indirizzo IPv4
	
	- Gateway IPv4
	
	- Subnet mask IPv4
	
	- Indirizzo IPv4 fisso per controller 0

	- Indirizzo IPv4 fisso per controller 1

## Passaggi successivi

- Per configurare le interfacce di rete diverse da DATA 0, è possibile utilizzare la [pagina di configurazione nel portale di Azure classico](storsimple-modify-device-config.md). 

- Se si riscontrano problemi durante la configurazione delle interfacce di rete, fare riferimento a [Risoluzione dei problemi di distribuzione](storsimple-troubleshoot-deployment.md).

<!---HONumber=AcomDC_0121_2016-->