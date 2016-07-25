<properties
 pageTitle="Abilitare i dispositivi gestiti protetti da un gateway IoT | Microsoft Azure"
 description="Argomento della Guida che descrive l'uso di un gateway IoT creato con Gateway SDK e dei dispositivi gestiti dall'hub IoT."
 services="iot-hub"
 documentationCenter=""
 authors="chipalost"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="cstreet"/>
 
# Abilitare i dispositivi gestiti protetti da un gateway IoT

## Isolamento dei dispositivi di base

Le organizzazioni usano spesso i gateway IoT per aumentare la sicurezza complessiva delle soluzioni IoT. Alcuni dispositivi devono inviare dati al cloud, ma non sono in grado di proteggersi dalle minacce in Internet. È possibile proteggere questi dispositivi dai thread esterni usando un gateway per la comunicazione con il mondo esterno.

Il gateway rappresenta il confine tra un ambiente protetto e la rete Internet aperta. I dispositivi comunicano con il gateway e il gateway passa i messaggi alla destinazione cloud corretta. Il gateway fornisce protezione avanzata per i thread esterni, blocca le richieste non autorizzate, consente il traffico in ingresso autorizzato e inoltra il traffico in ingresso al dispositivo corretto.

![][1]

È anche possibile schermare con un gateway i dispositivi in grado di proteggersi autonomamente per fornire un ulteriore livello di sicurezza. In questo scenario è sufficiente mantenere il sistema operativo del gateway aggiornato contro le vulnerabilità più recenti, invece dei sistemi operativi dei singoli dispositivi.

## Isolamento più intelligence

Un router con protezione avanzata è un gateway che consente di isolare semplicemente i dispositivi. Le soluzioni IoT, però, richiedono spesso che un gateway fornisca altre funzionalità oltre al semplice isolamento dei dispositivi, ad esempio la gestione dei dispositivi dal cloud. Per la parte di gestione del cloud della soluzione, è possibile usare LWM2M, un protocollo di gestione dei dispositivi standard, ma i dispositivi inviano dati di telemetria con un protocollo non abilitato per TCP/IP. Inoltre, i dispositivi producono grandi quantità di dati, mentre lo scopo dell'utente è solo di caricare un subset filtrato dei dati di telemetria. È possibile compilare una soluzione che incorpora un gateway IoT in grado di gestire due flussi distinti di dati. Il gateway deve:

-   Riconoscere i dati di **telemetria**, filtrarli e quindi caricarli nel cloud con il gateway. Il gateway non è più un semplice router che inoltra semplicemente i dati dal dispositivo al cloud.

-   Scambiare semplicemente i **dati di gestione dei dispositivi LWM2M** tra i dispositivi e il cloud. Il gateway non deve riconoscere i dati in ingresso, ma solo verificare che vengano trasmessi dai dispositivi al cloud e viceversa.

Questo scenario viene illustrato nell'immagine seguente:

![][2]

## Soluzione: gestione dei dispositivi IoT di Azure e Gateway SDK 

La versione di anteprima pubblica della [gestione dei dispositivi IoT di Azure][lnk-device-management] e versione beta di [Azure IoT Gateway SDK] consentono questo scenario. Il gateway gestisce ogni flusso di dati con le modalità seguenti:

-   **Telemetria**: è possibile usare Gateway SDK per compilare una pipeline che riconosca, filtri e invii i dati di telemetria al cloud. Gateway SDK fornisce il codice che implementa parti di questa pipeline al posto dello sviluppatore. Per altre informazioni sull'architettura dell'SDK, vedere l'esercitazione [IoT Gateway SDK - Introduzione][lnk-gateway-get-started].

-   **Gestione dei dispositivi**: la gestione dei dispositivi di Azure fornisce un client LWM2M che viene eseguito sul dispositivo, nonché un'interfaccia cloud per inviare i comandi di gestione al dispositivo.
    
    Non è necessaria una logica speciale per il gateway perché i dati LWM2M scambiati tra il dispositivo e l'hub IoT non devono essere elaborati. È possibile abilitare la condivisione della connessione Internet nel gateway, una funzionalità di molti sistemi operativi moderni, per consentire lo scambio di dati LWM2M. È possibile scegliere il sistema operativo più adatto per questo scenario perché Gateway SDK supporta un'ampia gamma di sistemi operativi. Di seguito sono riportate le istruzioni per abilitare la condivisione della connessione Internet in [Windows 10] e [Ubuntu], due dei diversi sistemi operativi supportati.

La figura seguente illustra l'architettura di alto livello usata per abilitare questo scenario con la [gestione dei dispositivi IoT di Azure][lnk-device-management] e [Azure IoT Gateway SDK].

![][3]

## Passaggi successivi

Per informazioni su come usare Gateway SDK, vedere le esercitazioni seguenti:

- [IoT Gateway SDK - Introduzione in Linux][lnk-gateway-get-started]
- [IoT Gateway SDK: inviare messaggi da dispositivo a cloud con un dispositivo simulato usando Linux][lnk-gateway-simulated]

Per ulteriori informazioni sulla gestione dei dispositivi con l'hub IoT, vedere l'[introduzione alla libreria client di Gestione dei dispositivi dell'hub IoT di Azure][lnk-library-c].

Per esplorare ulteriormente le funzionalità dell'hub IoT, vedere:

- [Progettare una soluzione][lnk-design]
- [Guida per sviluppatori][lnk-devguide]
- [Simulazione di un dispositivo con Gateway SDK][lnk-gateway]
- [Utilizzo del portale di Azure per gestire l'hub IoT][lnk-portal]

<!-- Images and links -->
[1]: media/iot-hub-gateway-device-management/overview.png
[2]: media/iot-hub-gateway-device-management/manage.png
[Azure IoT Gateway SDK]: https://github.com/Azure/azure-iot-gateway-sdk/
[Windows 10]: http://windows.microsoft.com/it-IT/windows/using-internet-connection-sharing#1TC=windows-7
[Ubuntu]: https://help.ubuntu.com/community/Internet/ConnectionSharing
[3]: media/iot-hub-gateway-device-management/manage_2.png
[lnk-gateway-get-started]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-gateway-simulated]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-device-management]: iot-hub-device-management-overview.md

[lnk-tutorial-twin]: iot-hub-device-management-device-twin.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-tutorial-jobs]: iot-hub-device-management-device-jobs.md
[lnk-dm-gateway]: iot-hub-gateway-device-management.md
[lnk-library-c]: iot-hub-device-management-library.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0713_2016-->