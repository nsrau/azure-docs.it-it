<properties
   pageTitle="Installare l'aggiornamento 1.2 nel dispositivo StorSimple | Microsoft Azure"
   description="Illustra come installare l'aggiornamento 1.2 di StorSimple serie 8000 sul dispositivo StorSimple serie 8000."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/22/2016"
   ms.author="alkohli" />

# Installare l'aggiornamento 1.2 nel dispositivo StorSimple

## Overview

In questa esercitazione viene illustrato come installare l'aggiornamento 1.2 in un dispositivo StorSimple in cui è in esecuzione una versione del software prima dell'aggiornamento 1. L'esercitazione illustra anche le ulteriori procedure richieste per l'aggiornamento quando un gateway è configurato su un'interfaccia di rete diversa da DATA 0 del dispositivo StorSimple.

L'aggiornamento 1.2 include aggiornamenti del software del dispositivo, aggiornamenti del driver LSI e aggiornamenti del firmware del disco. Gli aggiornamenti del software e del driver LSI non sono problematici e possono essere applicati attraverso il portale di Azure classico. Gli aggiornamenti del firmware del disco sono problematici e possono essere applicati solo tramite l'interfaccia di Windows PowerShell del dispositivo.

In base alla versione eseguita dal dispositivo, è possibile determinare se verrà applicato l'aggiornamento 1.2. È possibile verificare la versione del software del dispositivo passando alla sezione **riepilogo rapido** del **Dashboard** del dispositivo.

</br>

| Se è in esecuzione la versione del software... | Cosa accade nel portale? |
|---------------------------------|--------------------------------------------------------------|
| Versione - GA | Se è in esecuzione la versione finale (GA), non applicare questo aggiornamento. Contattare il [supporto tecnico Microsoft](storsimple-contact-microsoft-support.md) per aggiornare il dispositivo.|
| Aggiornamento 0.1 | Il portale applica l'aggiornamento 1.2. |
| Aggiornamento 0.2 | Il portale applica l'aggiornamento 1.2. |
| Aggiornamento 0.3 | Il portale applica l'aggiornamento 1.2. |
| Aggiornamento 1 | Questo aggiornamento non sarà disponibile. |
| Aggiornamento 1.1 | Questo aggiornamento non sarà disponibile. |

</br>

> [AZURE.IMPORTANT]

> -  L'aggiornamento 1.2 potrebbe non essere immediatamente visibile perché viene effettuata un'implementazione graduale degli aggiornamenti. Provare a cercare nuovamente l'aggiornamento dopo qualche giorno perché verrà presto reso disponibile.
> - Questo aggiornamento include una serie di controlli preliminari automatici e manuali per determinare l'integrità del dispositivo in termini di connettività di stato e di rete hardware. Questi controlli preliminari vengono eseguiti solo se si applicano gli aggiornamenti dal portale di Azure classico.
> - Si consiglia di installare gli aggiornamenti software e driver tramite il portale di Azure classico. Passare all'interfaccia di Windows PowerShell del dispositivo (per installare gli aggiornamenti) solo se il gateway di pre-aggiornamento ha esito negativo nel portale. L'installazione di tutti gli aggiornamenti, inclusi gli aggiornamenti di Windows, potrebbe richiedere fino a 5-10 ore. Gli aggiornamenti in modalità di manutenzione devono essere installati tramite l'interfaccia di Windows PowerShell del dispositivo. Dal momento che si tratta di aggiornamenti problematici, comporteranno un periodo di inattività per il dispositivo.

[AZURE.INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## Installare l'aggiornamento 1.2 tramite il portale di Azure classico.

Seguire questa procedura per aggiornare il dispositivo a [Aggiornamento 1.2](storsimple-update1-release-notes.md). Usare questa procedura solo se è presente un gateway configurato sull'interfaccia di rete DATA 0 sul dispositivo.

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

12. Verificare che nel dispositivo sia in esecuzione l'**aggiornamento 1.2 della serie 8000 di StorSimple (6.3.9600.17584)**. Inoltre, è necessario modificare la **data dell'ultimo aggiornamento**. Saranno anche disponibili gli aggiornamenti in modalità manutenzione. Questo messaggio potrebbe essere visualizzato fino a 24 ore dopo l’installazione degli aggiornamenti.

    Gli aggiornamenti in modalità manutenzione sono aggiornamenti problematici che comportano tempi di inattività del dispositivo e possono essere applicati solo tramite l'interfaccia di Windows PowerShell del dispositivo.

    ![Pagina di manutenzione](./media/storsimple-install-update-1/InstallUpdate12_10M.png "Pagina di manutenzione")

13. Scaricare gli aggiornamenti in modalità manutenzione utilizzando la procedura indicata in [Scaricare gli aggiornamenti rapidi](#to-download-hotfixes) per cercare e scaricare KB3063416, che installa gli aggiornamenti del firmware del disco (gli altri aggiornamenti devono essere già installati a questo punto).

13. Seguire i passaggi elencati nella sezione [Installare e verificare gli aggiornamenti rapidi in modalità manutenzione](#to-install-and-verify-maintenance-mode-hotfixes) per installare gli aggiornamenti in modalità manutenzione.

14. Nel portale di Azure classico, passare alla pagina **Manutenzione** e, nella parte inferiore della pagina, fare clic su **Cerca aggiornamenti** per verificare la presenza di eventuali aggiornamenti di Windows e quindi fare clic su **Installa aggiornamenti**. Attendere che tutti gli aggiornamenti vengano installati correttamente.



## Installare l'aggiornamento 1.2 in un dispositivo con un gateway configurato per un'interfaccia di rete non DATA 0

Usare questa procedura solo se la verifica del gateway non riesce quando si cerca di installare gli aggiornamenti tramite il portale di Azure classico. La verifica non riesce quando un gateway è assegnato a un'interfaccia di rete non DATA 0e sul dispositivo è in esecuzione una versione del software precedente all'aggiornamento 1. Se il dispositivo non dispone di un gateway su un'interfaccia di rete 0 non di dati, è possibile aggiornare il dispositivo direttamente dal portale di Azure classico. Vedere [Installare l'aggiornamento 1.2 tramite il portale di Azure classico](#install-update-1.2-via-the-azure-classic-portal).

Le versioni software che possono essere aggiornate usando questo metodo sono Aggiornamento 0.1, Aggiornamento 0.2 e Aggiornamento 0.3.


> [AZURE.IMPORTANT]
>
> - Se il dispositivo esegue la versione finale (GA), contattare [il supporto tecnico Microsoft](storsimple-contact-microsoft-support.md) per assistenza relativa all'aggiornamento.
> - Questa procedura deve essere eseguita solo una volta per applicare l'aggiornamento 1.2. È possibile utilizzare il portale di Azure classico per applicare gli aggiornamenti successivi.

Se sul dispositivo è in esecuzione un software precedente all'aggiornamento 1 e ha un gateway impostato per un'interfaccia di rete diversa da DATA 0, è possibile applicare l'aggiornamento 1.2 nei due modi seguenti:

- **Opzione 1**: scaricare l'aggiornamento e applicarlo usando il cmdlet `Start-HcsHotfix` dall'interfaccia Windows PowerShell del dispositivo. Questo è il metodo consigliato. **Non usare questo metodo per applicare l’aggiornamento 1.2 se il dispositivo esegue l’aggiornamento 1.0 o 1.1.**

- **Opzione 2**: rimuovere la configurazione del gateway e installare l'aggiornamento direttamente dal portale di Azure classico.


Nelle sezioni seguenti vengono fornite istruzioni dettagliate per ciascuno di essi.

## Opzione 1: usare Windows PowerShell per StorSimple per applicare l'aggiornamento 1.2 come hotfix

Usare questa procedura solo se si esegue l'aggiornamento 0.1, 0.2, 0.3 e se la verifica del gateway non è riuscita quando si è provato a installare gli aggiornamenti tramite il portale di Azure classico. Se si esegue la versione finale (GA), contattare il [supporto tecnico Microsoft](storsimple-contact-microsoft-support.md) per aggiornare il dispositivo.

Per installare l'Aggiornamento 1.2 come un aggiornamento rapido, è necessario scaricare e installare i seguenti aggiornamenti rapidi:

| Ordine | KB | Descrizione | Tipo di aggiornamento |
|--------|-----------|-------------------------|------------- |
| 1 | KB3063418 | Aggiornamento software | Normale |
| 2 | KB3043005 | Aggiornamento del controller LSI SAS | Normale |
| 3 | KB3063416 | Firmware del disco | Manutenzione |

Prima di utilizzare questa procedura per applicare l'aggiornamento, verificare quanto segue:

- Entrambi i controller di dispositivo sono in linea.

Seguire questa procedura per applicare l'aggiornamento 1.2 **Il completamento degli aggiornamenti potrebbe richiedere circa 2 ore (circa 30 minuti per il software, 30 minuti per il driver, 45 minuti per il firmware del disco).**

[AZURE.INCLUDE [installazione di storsimple-opzione1 di aggiornamento](../../includes/storsimple-install-update-option1.md)]


## Opzione 2: usare il portale di Azure classico per applicare l'aggiornamento 1.2 dopo aver rimosso la configurazione del gateway

Questa procedura si applica solo ai dispositivi StorSimple che eseguono una versione del software precedente all'aggiornamento 1 e hanno un gateway impostato su un'interfaccia di rete diversa da DATA 0. È necessario deselezionare l'impostazione gateway prima di applicare l'aggiornamento.

L'aggiornamento potrebbe richiedere alcune ore. Se l'host si trovano in subnet diverse, rimuovendo la configurazione del gateway sulle interfacce iSCSI può comportare tempi di inattività. Si consiglia di configurare DATA 0 per il traffico iSCSI per ridurre il tempo di inattività.

Seguire la procedura seguente per disabilitare l'interfaccia di rete con il gateway e quindi applicare l'aggiornamento.

[AZURE.INCLUDE [installazione di storsimple-opzione2 di aggiornamento](../../includes/storsimple-install-update-option2.md)]

[AZURE.INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]


## Passaggi successivi

Altre informazioni sulla [versione dell'aggiornamento 1.2](storsimple-update1-release-notes.md)

<!---HONumber=AcomDC_0824_2016-->