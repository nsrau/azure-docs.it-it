<properties 
   pageTitle="Gestire i contenitori del volume di StorSimple"
   description="Viene illustrato come è possibile utilizzare la pagina di contenitori del volume di servizio StorSimple Manager per aggiungere, modificare o eliminare un contenitore del volume."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/13/2015"
   ms.author="v-sharos" />

# Gestire i contenitori del volume di StorSimple

## Panoramica

In questa esercitazione viene illustrato come utilizzare il servizio StorSimple Manager per creare e gestire contenitori dei volumi StorSimple.

Un contenitore del volume in un dispositivo StorSimple di Microsoft Azure contiene uno o più volumi che condividono l'account di archiviazione, la crittografia e le impostazioni del consumo di larghezza di banda. Un dispositivo può avere più contenitori del volume per tutti i volumi.

Un contenitore del volume ha i seguenti attributi:

- **Volumi** – i thin provisioning volumi StorSimple di cui sono contenuti all'interno del contenitore del volume. Un contenitore del volume può contenere fino a 256 volumi StorSimple di thin provisioning.

- **Crittografia** : una chiave di crittografia che può essere definita per ogni contenitore del volume. Questa chiave viene utilizzata per crittografare i dati inviati dal dispositivo StorSimple nel cloud. Una chiave a livello militare AES-256 bit viene utilizzata con la chiave immesso dall'utente. Per proteggere i dati, è consigliabile abilitare sempre la crittografia di archiviazione cloud.

- **Account di archiviazione** : l'account di archiviazione collegato al provider di servizi di archiviazione cloud. Tutti i volumi che risiedono in un contenitore di volumi condividono questo account di archiviazione. È possibile scegliere un account di archiviazione da un elenco esistente o creare un nuovo account quando si crea il contenitore del volume e quindi specificare le credenziali di accesso per l'account.

- **Larghezza di banda cloud** : larghezza di banda usata dal dispositivo quando i dati dal dispositivo vengono inviati al cloud. È possibile applicare un controllo della larghezza di banda specificando un valore compreso tra 1 e 1000 Mbps quando si definisce questo contenitore. Se si desidera che il dispositivo usi tutti larghezza di banda disponibile, impostare questo campo su illimitata. È inoltre possibile creare e applicare un modello di larghezza di banda per l'allocazione della larghezza di banda in base alla pianificazione.

![Andare alla pagina Contenitori di volumi.](./media/storsimple-manage-volume-containers/HCS_VolumeContainersPage.png)

Questa procedure seguenti viene illustrato come utilizzare quest'ultimo **contenitori del Volume** per completare le operazioni comuni seguenti:

- Aggiungere un contenitore di volumi 
- Modificare un contenitore di volumi 
- Eliminare un contenitore di volumi 

## Aggiungere un contenitore di volumi

Procedere come descritto di seguito per creare un account di archiviazione.

[AZURE.INCLUDE [storsimple-Aggiungi-volume-contenitore](../../includes/storsimple-add-volume-container.md)]


## Modificare un contenitore di volumi

Eseguire i passaggi seguenti per modificare un contenitore del volume.

[AZURE.INCLUDE [storsimple-modifica-volume-container](../../includes/storsimple-modify-volume-container.md)]


## Eliminare un contenitore di volumi

Un contenitore del volume ha volumi all'interno di esso. Può essere eliminato solo se sono stati eliminati tutti i volumi in esso contenuti. Eseguire la procedura seguente per eliminare un contenitore del volume.

[AZURE.INCLUDE [eliminazione di storsimple-contenitore di volumi](../../includes/storsimple-delete-volume-container.md)]

## Passaggi successivi

Ulteriori informazioni su [la gestione di volumi StorSimple](storsimple-manage-volumes.md).
 

<!---HONumber=July15_HO2-->