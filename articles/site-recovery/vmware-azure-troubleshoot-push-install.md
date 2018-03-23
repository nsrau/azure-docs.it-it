---
title: Risoluzione dei problemi di Azure Site Recovery da VMware ad Azure | Microsoft Docs
description: Correggere gli errori durante la replica di macchine virtuali di Azure.
services: site-recovery
author: anoopkv
manager: gauravd
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: bdd5d87670ddb26d2c4474a319fe19bef1db62f5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>Risolvere i problemi di installazione push del servizio Mobility

Questo articolo descrive come risolvere i problemi più comuni che possono verificarsi quando si prova a installare il servizio Mobility di Azure Site Recovery nel server di origine per abilitare la protezione.

## <a name="error-78007---the-requested-operation-could-not-be-completed"></a>Errore 78007 - Non è stato possibile completare l'operazione richiesta
Questo errore può essere generato dal servizio per diversi motivi. Scegliere l'errore del provider corrispondente per risolvere il problema.

* [Errore 95103](#error-95103---protection-could-not-be-enabled-ep0854) 
* [Errore 95105](#error-95105---protection-could-not-be-enabled-ep0856) 
* [Errore 95107](#error-95107---protection-could-not-be-enabled-ep0858) 
* [Errore 95108](#error-95108---protection-could-not-be-enabled-ep0859) 
* [Errore 95117](#error-95117---protection-could-not-be-enabled-ep0865) 
* [Errore 95213](#error-95213---protection-could-not-be-enabled-ep0874) 
* [Errore 95224](#error-95224---protection-could-not-be-enabled-ep0883) 
* [Errore 95265](#error-95265---protection-could-not-be-enabled-ep0902) 


## <a name="error-95105---protection-could-not-be-enabled-ep0856"></a>Errore 95105 - Non è stato possibile abilitare la protezione (EP0856)

**Codice errore** | **Possibili cause** | **Indicazioni specifiche per l'errore**
--- | --- | ---
95105 </br>**Messaggio:** Push installation of Mobility Service to the source machine failed with error code **EP0856**. <br> Either **File and Printer Sharing** isn't allowed on the source machine or there are network connectivity problems between the process server and the source machine (L'installazione push del servizio Mobility nel computer di origine non è riuscita con codice errore EP0856. Condivisione file e stampanti non è consentito nel computer di origine oppure ci sono problemi di connettività tra il server di elaborazione e il computer di origine).| La funzionalità **Condivisione file e stampanti** non è abilitata. | Consentire **Condivisione file e stampanti** nel computer di origine in Windows Firewall. Nel computer di origine in **Windows Firewall** > **Consenti app o funzionalità attraverso Windows Firewall** selezionare **Condivisione file e stampanti** per tutti i profili. </br> Verificare inoltre i prerequisiti seguenti per completare correttamente l'installazione push.<br> Altre informazioni sulla [risoluzione dei problemi di WMI](#troubleshoot-wmi-issues).


## <a name="error-95107---protection-could-not-be-enabled-ep0858"></a>Errore 95107 - Non è stato possibile abilitare la protezione (EP0858)

**Codice errore** | **Possibili cause** | **Indicazioni specifiche per l'errore**
--- | --- | ---
95107 </br>**Messaggio:** Push installation of Mobility Service to the source machine failed with error code **EP0858**. <br> Either the credentials provided to install Mobility Service are incorrect or the user account has insufficient privileges (L'installazione push del servizio Mobility nel computer di origine non è riuscita con codice errore EP0858. Le credenziali specificate per installare il servizio Mobility non sono corrette oppure l'account utente ha privilegi insufficienti). | Le credenziali utente specificate per installare il servizio Mobility nel computer di origine non sono corrette. | Verificare che le credenziali utente specificate per il computer di origine nel server di configurazione siano corrette. <br> Per aggiungere o modificare credenziali utente, passare al server di configurazione e selezionare **Cspsconfigtool** > **Gestisci account**. </br> Verificare inoltre i [prerequisiti](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery) seguenti per completare correttamente l'installazione push.


## <a name="error-95117---protection-could-not-be-enabled-ep0865"></a>Errore 95117 - Non è stato possibile abilitare la protezione (EP0865)

**Codice errore** | **Possibili cause** | **Indicazioni specifiche per l'errore**
--- | --- | ---
95117 </br>**Messaggio:** Push installation of Mobility Service to the source machine failed with error code **EP0865**. <br> Either the source machine isn't running or there are network connectivity problems between the process server and the source machine (L'installazione push del servizio Mobility nel computer di origine non è riuscita con codice errore EP0865. Il computer di origine non è in esecuzione oppure ci sono problemi di connettività di rete tra il server di elaborazione e il computer di origine). | Problemi di connettività di rete tra il server di elaborazione e il server di origine. | Controllare la connettività tra il server di elaborazione e il server di origine. </br> Verificare inoltre i [prerequisiti](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery) seguenti per completare correttamente l'installazione push.|

## <a name="error-95103---protection-could-not-be-enabled-ep0854"></a>Errore 95103 - Non è stato possibile abilitare la protezione (EP0854)

**Codice errore** | **Possibili cause** | **Indicazioni specifiche per l'errore**
--- | --- | ---
95103 </br>**Messaggio:** Push installation of Mobility Service to the source machine failed with error code **EP0854**. <br> Either Windows Management Instrumentation (WMI) isn't allowed on the source machine or there are network connectivity problems between the process server and the source machine (L'installazione push del servizio Mobility nel computer di origine non è riuscita con codice errore EP0854. La funzionalità Strumentazione gestione Windows non è consentita nel computer di origine oppure ci sono problemi di connettività tra il server di elaborazione e il computer di origine).| La funzionalità Strumentazione gestione Windows è bloccata in Windows Firewall. | Consentire Strumentazione gestione Windows in Windows Firewall. In **Windows Firewall** > **Consenti app o funzionalità attraverso Windows Firewall** selezionare **Strumentazione gestione Windows** per tutti i profili. </br> Verificare inoltre i [prerequisiti](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery) seguenti per completare correttamente l'installazione push.|

## <a name="error-95213---protection-could-not-be-enabled-ep0874"></a>Errore 95213 - Non è stato possibile abilitare la protezione (EP0874)

**Codice errore** | **Possibili cause** | **Indicazioni specifiche per l'errore**
--- | --- | ---
95213 </br>**Messaggio:** Installation of Mobility Service to the source machine %SourceIP; failed with error code **EP0874** (L'installazione del servizio Mobility nel computer di origine %SourceIP; non è riuscita con codice errore EP0874). <br> | La versione del sistema operativo nel computer di origine non è supportata. <br>| Verificare che la versione del sistema operativo del computer di origine sia supportata. Leggere la [matrice di supporto](https://aka.ms/asr-os-support). </br> Verificare inoltre i [prerequisiti](https://aka.ms/pushinstallerror) seguenti per completare correttamente l'installazione push.| 


## <a name="error-95108---protection-could-not-be-enabled-ep0859"></a>Errore 95108 - Non è stato possibile abilitare la protezione (EP0859)

**Codice errore** | **Possibili cause** | **Indicazioni specifiche per l'errore**
--- | --- | ---
95108 </br>**Messaggio:** Push installation of Mobility Service to the source machine failed with error code **EP0859**. <br>| Either the credentials provided to install Mobility Service are incorrect or the user account has insufficient privileges (L'installazione push del servizio Mobility nel computer di origine non è riuscita con codice errore EP0859. Le credenziali specificate per installare il servizio Mobility non sono corrette oppure l'account utente ha privilegi insufficienti). <br>| Verificare che le credenziali specificate siano le credenziali dell'account di **radice**. Per aggiungere o modificare le credenziali utente, passare al server di configurazione e selezionare l'icona del collegamento **Cspsconfigtool** sul desktop. Selezionare **Gestisci account** per aggiungere o modificare le credenziali.|

## <a name="error-95265---protection-could-not-be-enabled-ep0902"></a>Errore 95265 - Non è stato possibile abilitare la protezione (EP0902)

**Codice errore** | **Possibili cause** | **Indicazioni specifiche per l'errore**
--- | --- | ---
95265 </br>**Messaggio:** Push installation of Mobility Service to the source machine succeeded but the source machine requires a restart for some system changes to take effect (L'installazione push del servizio Mobility nel computer di origine è stata completata ma è necessario riavviare per rendere effettive alcune modifiche di sistema). <br>| Una versione precedente del servizio Mobility è già installata nel server.| La replica della macchina virtuale continua senza problemi.<br> Riavviare il server durante la successiva finestra di manutenzione per ottenere i vantaggi offerti dai nuovi miglioramenti nel servizio Mobility.|


## <a name="error-95224---protection-could-not-be-enabled-ep0883"></a>Errore 95224 - Non è stato possibile abilitare la protezione (EP0883)

**Codice errore** | **Possibili cause** | **Indicazioni specifiche per l'errore**
--- | --- | ---
95224 </br>**Messaggio:** Push installation of Mobility Service to the source machine %SourceIP; failed with error code **EP0883** (L'installazione push del servizio Mobility nella macchina virtuale di origine %SourceIP; non è riuscita con codice errore EP0883). Un riavvio del sistema per un'installazione o un aggiornamento precedente è in sospeso.| Il sistema non è stato riavviato durante la disinstallazione di una versione precedente o incompatibile del servizio Mobility.| Verificare che nessuna versione del servizio Mobility sia presente sul server. <br> Riavviare il server ed eseguire di nuovo il processo di abilitazione della protezione.|

## <a name="resource-to-troubleshoot-push-installation-problems"></a>Risorse per risolvere i problemi di installazione push

#### <a name="troubleshoot-file-and-print-sharing-issues"></a>Risolvere i problemi di condivisione file e stampanti
* [Abilitare o disabilitare la condivisione di file con Criteri di gruppo](https://technet.microsoft.com/library/cc754359(v=ws.10).aspx)
* [Abilitare la condivisione di file e stampanti tramite Windows Firewall](https://technet.microsoft.com/library/ff633412(v=ws.10).aspx)

#### <a name="troubleshoot-wmi-issues"></a>Risoluzione dei problemi WMI
* [Test WMI di base](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [Risoluzione dei problemi WMI](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [Risoluzione dei problemi con gli script WMI e i servizi WMI](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="next-steps"></a>Passaggi successivi

[Informazioni su come](vmware-azure-tutorial.md) configurare il ripristino di emergenza per le macchine virtuali VMware.