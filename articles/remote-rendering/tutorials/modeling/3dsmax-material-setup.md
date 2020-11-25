---
title: Configurare i materiali PBR in 3ds Max
description: Illustra come configurare i materiali PBR (Physically Based Rendering) in 3ds Max e come esportarli in formato FBX.
author: FlorianBorn71
ms.author: flborn
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: 12407d6344c69c747230e9db6fa4d53b4520dc82
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020280"
---
# <a name="tutorial-set-up-physically-based-rendering-materials-in-3ds-max"></a>Esercitazione: Configurare i materiali PBR (Physically Based Rendering) in 3ds Max

## <a name="overview"></a>Panoramica
In questa esercitazione si apprenderà come:

>[!div class="checklist"]
>
> * Assegnare materiali con illuminazione avanzata agli oggetti in una scena.
> * Gestire la creazione di istanze di oggetti e materiali.
> * Esportare una scena in formato FBX e selezionare opzioni importanti.

> [!Note]
> La procedura descritta in questa esercitazione funziona in 3ds Max 2019 e 3ds Max 2020.
> Una modifica del modo in cui 3ds Max 2021 esporta mappe Bump significa che le mappe normali non verranno trovate dal servizio di conversione se viene usata tale versione.

La creazione di [materiali basati su PBR (Physically Based Rendering)](../../overview/features/pbr-materials.md) in 3ds Max è un'attività semplice. È simile sotto molti aspetti alla configurazione di materiali basati su PBR in altre app per la creazione di contenuti come Maya. Questa esercitazione è una guida alla configurazione di una funzione shader PBR di base e all'esportazione in formato FBX per i progetti di Rendering remoto di Azure.

La scena di esempio dell'esercitazione contiene una serie di oggetti poligono a cui sono assegnati materiali diversi, ossia legno, metallo, metallo verniciato, plastica e gomma. In generale, ogni materiale contiene tutte o la maggior parte delle trame seguenti:

* **Albedo**, che è la mappa colori del materiale, denominata anche **Diffuse** e **BaseColor**.
* **Metalness**, che determina se un materiale è metallico e quali parti sono metalliche. 
* **Roughness**, che determina quanto è ruvida o liscia una superficie,
determinando di conseguenza la nitidezza o la sfocatura dei riflessi e delle aree di massima luce sulla superficie stessa.
* **Normal**, che aggiunge dettagli a una superficie senza bisogno di aggiungere altri poligoni. Esempi di dettaglio possono sono erosioni e ammaccature su una superficie metallica o venature su legno.
* **Ambient Occlusion**, che viene usata per aggiungere ombreggiature sfumate e ombre da contatto a un modello. Si tratta di una mappa in scala di grigi che indica quali aree del modello sono totalmente illuminate (bianche) o totalmente in ombra (nere).

## <a name="prepare-the-scene"></a>Preparare la scena
In 3ds Max il processo di configurazione di un materiale PBR è il seguente.

Per iniziare, verranno creati diversi oggetti scatola, ognuno dei quali rappresenta un tipo di materiale diverso.

>[!TIP]
>Prima di iniziare a creare asset per Rendering remoto, vale la pena sottolineare che questo servizio usa il metro come unità di misura.  
>
>Quindi, è consigliabile impostare le unità di sistema della scena su metri. È anche consigliabile impostare **Unità** su metri nelle impostazioni di esportazione in FBX quando si esporta una scena.

Lo screenshot seguente illustra la procedura per impostare le unità di sistema su metri in 3ds Studio Max. 

1. Nel menu principale scegliere **Customize** > **Units Setup** > **System Units Setup** (Personalizza > Configurazione unità > Configurazione unità di sistema). In **System Unit Scale** (Scala unità di sistema) selezionare **Meters** (Metri): ![Screenshot che illustra come impostare le unità di sistema.](media/3dsmax/system-units.jpg)

1. È ora possibile iniziare a creare i modelli. Nella scena di esempio vengono creati diversi oggetti scatola, ognuno dei quali rappresenta un tipo di materiale diverso, ad esempio metallo, gomma, plastica e così via. 

   >[!TIP]
   >Quando si creano asset, è consigliabile assegnare nomi appropriati man mano che si procede. In questo modo sarà più facile trovarli successivamente se la scena contiene molti oggetti.

1. Rinominare gli oggetti, come illustrato nello screenshot seguente: 

   ![Screenshot che illustra come rinominare gli oggetti.](media/3dsmax/rename-objects.jpg)

## <a name="assign-materials"></a>Assegnare i materiali

Dopo aver creato alcuni oggetti nella scena, in questo caso alcuni cubi, è possibile iniziare la configurazione del materiale PBR:

1. Sulla barra degli strumenti principale selezionare l'icona **Material Editor** (Editor dei materiali), come illustrato nello screenshot seguente. È anche possibile premere **M** sulla tastiera per aprire l'editor. L'editor dei materiali include due modalità che è possibile selezionare nell'elenco **Modes**: la modalità **Compact Material Editor** (Editor di materiali compatti) e la modalità **Slate Material Editor** (Editor di materiali slate). Poiché questa scena è relativamente semplice, verrà usata la modalità compatta.

1. Nell'editor dei materiali verranno visualizzate diverse sfere, che rappresentano i materiali. Si assegnerà uno di questi materiali a ogni oggetto scatola della scena. Per assegnare i materiali, selezionare prima uno degli oggetti nel riquadro di visualizzazione principale. Quindi selezionare la prima sfera nell'editor dei materiali. Una volta assegnato a un oggetto, il materiale selezionato verrà evidenziato come illustrato nell'immagine seguente.

1. Selezionare **Assign Material to Selection** (Assegna materiale alla selezione) come illustrato di seguito. Il materiale è ora assegnato all'oggetto selezionato.

   ![Screenshot che illustra come assegnare materiali.](media/3dsmax/assign-material.jpg)

    Nell'editor dei materiali è possibile scegliere tra un'ampia varietà di tipi di materiale, a seconda delle esigenze. In genere, il tipo di materiale è impostato su **Standard** per impostazione predefinita. Si tratta di un materiale di base non adatto per la configurazione di PBR. Quindi è necessario sostituirlo con un materiale PBR. Il materiale preferito di 3ds Max per i progetti di Rendering remoto di Azure è Physical Material (Materiale fisico).

1. Nell'editor dei materiali selezionare la scheda **Standard**. In **Material/Map Browser** (Browser materiale/mappa) selezionare **Physical Material** (Materiale fisico). Questa azione converte il materiale **Standard** assegnato in un materiale fisico PBR.

   ![Screenshot che illustra come cambiare materiale.](media/3dsmax/physical-material.jpg)

    Nell'editor dei materiali vengono ora visualizzate le proprietà del materiale fisico, come illustrato nello screenshot seguente. È ora possibile iniziare ad assegnare trame all'asset.

   ![Screenshot che mostra l'elenco delle trame.](media/3dsmax/textures-list.jpg)

Come si può notare, è disponibile un'ampia gamma di mappe e trame che è possibile aggiungere al materiale. Per questa esercitazione vengono usati solo cinque slot di trame nel materiale.

>[!TIP]
>È consigliabile assegnare ai materiali nomi appropriati, come illustrato nello screenshot precedente.

La modalità di generazione delle trame può variare in base alle preferenze o all'utilizzo. Ad esempio, si potrebbe scegliere di usare le trame di rivestimento, che possono essere applicate a qualsiasi asset. Oppure può essere necessario specificare set di trame personalizzati per specifiche parti di un progetto o di un asset. Si possono usare trame di rivestimento generiche disponibili online o creare trame personalizzate con app come Photoshop, Quixel Suite e Substance Suite.

Prima di iniziare ad assegnare le trame, è necessario considerare le coordinate di trama degli asset (UVW). Si tratta di una procedura consigliata quando si applicano trame a un modello per assicurarne l'unwrapping. Le trame non vengono visualizzate correttamente senza un corretto unwrapping UV. È particolarmente importante ai fini di questa esercitazione, perché verrà usata una mappa di occlusione ambientale (AO, Ambient Occlusion) sul modello. Diversamente da Stingray Shader in Maya, il materiale fisico in 3ds Max non ha uno slot di trama AO dedicato. La mappa AO verrà quindi applicata a un altro slot. Per consentirne l'uso separatamente dalle altre trame (ad esempio le trame di rivestimento), le verrà assegnato un canale di mappe UVW autonomo. 

Per iniziare, assegnare un modificatore Unwrap UVW al modello, come illustrato nello screenshot seguente. 

- Nell'editor delle proprietà degli oggetti selezionati scegliere l'elenco dei modificatori. Nell'elenco a discesa visualizzato scorrere verso il basso e selezionare **Unwrap UVW**. Verrà applicato un modificatore Unwrap UVW all'asset.
![Screenshot che illustra come selezionare Unwrap UVW.](media/3dsmax/unwrap-modifier.jpg)

  Il canale di mappe è impostato su 1. In genere l'operazione principale di unwrapping viene eseguita nel canale di mappe 1. In questo caso, l'unwrapping dell'oggetto è stato eseguito senza sovrapposizione delle coordinate delle trame (UV).
![Screenshot che mostra le coordinate delle trame dopo l'unwrapping (UVW).](media/3dsmax/unwrapped-uvw.jpg)

Il passaggio successivo consiste nel creare un secondo canale mappe UV.

1. Chiudere l'editor UV se è aperto. Nella sezione **Channel** (Canale) del menu **Edit UVs** (Modifica UV) cambiare il numero di canale in **2**. Il canale di mappe 2 è quello previsto per le mappe di AO. 

1. Nella finestra di dialogo **Channel Change Warning** (Avviso cambio canale) è possibile selezionare **Move** (Sposta) per spostare le coordinate UV esistenti dal canale 1 al nuovo canale 2 oppure **Abandon** (Abbandona) per abbandonare le coordinate creando automaticamente un nuovo unwrapping UV. Selezionare **Abandon** solo se si prevede di creare un nuovo unwrapping UV per la mappa AO diverso dalle coordinate UV del canale di mappe 1, ad esempio se si vogliono usare trame di rivestimento nel canale 1. In questa esercitazione le coordinate UV verranno spostate dal canale 1 al canale 2 perché non è necessario modificare il nuovo canale UV.

   >[!NOTE]
   >Anche se l'unwrapping UV è stato copiato (spostato) dal canale di mappe 1 al canale di mappe 2, è possibile apportare le eventuali modifiche necessarie alle nuove coordinate UV del canale senza influire sul canale di mappe originale.

   ![Screenshot che mostra l'avviso di cambio canale.](media/3dsmax/channel-change.jpg)

Una volta creato il nuovo canale di mappe, è possibile tornare al materiale fisico nell'editor dei materiali e iniziare ad aggiungervi le trame. Prima di tutto verrà aggiunta la mappa AO perché è necessario completare un altro passaggio per consentirne il corretto funzionamento. Dopo aver collegato la mappa AO al materiale, è necessario configurarla per l'uso del canale di mappe 2.

Come già accennato, non esiste uno slot dedicato per le mappe AO nel materiale fisico di 3ds Max. La mappa AO verrà quindi applicata allo slot **Diffuse Roughness** (Ruvidità diffusa).

1. Nell'elenco **Generic Maps** (Mappe generiche) del materiale fisico fare clic su **No Map** (Nessuna mappa) per lo slot **Diffuse Roughness** (Ruvidità diffusa) e caricare la mappa AO.

1. Nelle proprietà delle trame AO si noterà che il canale di mappe è impostato su **1** per impostazione predefinita. Cambiare questo valore in **2**. Questa azione completa i passaggi necessari per aggiungere la mappa AO.

   >[!IMPORTANT]
   >È un passaggio importante, specialmente se le coordinate UV nel canale 2 sono diverse rispetto a quelle del canale 1, in quanto la mappa AO non verrà tracciata correttamente se si seleziona il canale errato.

   ![Screenshot che illustra come assegnare una mappa AO.](media/3dsmax/assign-ao-map.jpg)

A questo punto, al materiale PBR verrà assegnata la mappa normale. Questa azione è leggermente diversa dal processo in Maya. La mappa normale non viene applicata direttamente allo slot della mappa Bump. Non esiste uno slot di mappa normale nel materiale fisico di 3ds Max. La mappa normale viene invece aggiunta a un modificatore di mappe normale, che a sua volta è collegato allo slot normale.

1. Nella sezione **Special Maps** (Mappe speciali) delle proprietà del materiale fisico (nell'editor dei materiali) fare clic su **No Map** (Nessuna mappa) accanto a **Bump Map** (Mappa Bump). 

1. Nel **browser di materiali/mappe** individuare e fare clic su **Normal Bump** (Bump normale). Verrà aggiunto un modificatore **Normal Bump** al materiale.

1. Nel modificatore **Normal Bump** selezionare **No Map** accanto a **Normale**. Individuare e caricare la mappa normale.

1. Verificare che il metodo sia impostato su **Tangent** (Tangente). Dovrebbe esserlo per impostazione predefinita. Se necessario, impostare **Flip Green (Y)** (Capovolgi verde).

   ![Screenshot che mostra come selezionare Bump normale.](media/3dsmax/normal-bump.jpg)
   ![Screenshot che mostra il caricamento della mappa normale.](media/3dsmax/load-normal-map.jpg)

Una volta assegnata correttamente la mappa normale, è possibile procedere con l'assegnazione delle trame rimanenti per completare la configurazione del materiale fisico. Il processo è semplice. Non ci sono impostazioni particolari da considerare. Lo screenshot seguente mostra il set completo di trame assegnate al materiale: 

![Screenshot che mostra il set completo di trame assegnate al materiale.](media/3dsmax/all-textures.jpg)

Una volta creati e configurati i materiali PBR, è opportuno creare istanze degli oggetti nella scena. Creare istanze di oggetti simili nella scena, come dadi, bulloni, viti e rondelle. Tutti gli oggetti uguali possono produrre un risparmio significativo in termini di dimensioni dei file. A ogni istanza di un oggetto master è possibile assegnare una scala, una rotazione e trasformazioni specifiche, in modo da poterla inserire nella scena in base alle esigenze. In 3ds Max il processo di creazione di istanze è semplice.

1. Nel riquadro di visualizzazione principale selezionare l'oggetto o gli oggetti da esportare.

1. Tenere premuto **MAIUSC** e trascinare gli asset verso l'alto usando lo strumento di trasformazione (spostamento). 

1. Nella finestra di dialogo **Clone Options** (Opzioni di clonazione) impostare **Object** (Oggetto) su **Instance** (Istanza) e selezionare **OK**:

   ![Screenshot della finestra di dialogo di opzioni di clonazione.](media/3dsmax/instance-object.jpg)

Questa azione crea un'istanza dell'oggetto che può essere spostata, ruotata o dimensionata indipendentemente dall'oggetto padre e da altre istanze di tale oggetto.

>[!IMPORTANT]
>Le eventuali modifiche apportate a un'istanza in modalità oggetto secondario verranno trasmesse a tutte le istanze dell'oggetto. Quindi, se si usano componenti di un'istanza di un oggetto, come i vertici e le facce dei poligoni, assicurarsi di voler effettivamente apportare le modifiche a tutte le istanze. Tenere presente che qualsiasi oggetto di cui sono state create istanze può essere reso un oggetto univoco in qualsiasi momento. 

>[!TIP]
>È consigliabile creare istanze nella scena man mano che si procede. La sostituzione successiva di copie con istanze degli oggetti è un'operazione difficile. 

Un aspetto finale da considerare prima di passare al processo di esportazione è il modo in cui è creare un pacchetto della scena o dell'asset per la condivisione. Idealmente, se si passa l'asset a un cliente o a un membro del team, si vuole che sia in grado di aprirlo e visualizzarlo nel modo corretto con il minimo sforzo. È quindi importante che i percorsi delle trame degli asset rimangano relativi al file della scena. Se questi percorsi puntano a un'unità locale o a un percorso assoluto, non verranno caricati nella scena se aperti in un altro computer, nemmeno se il file con estensione max si trova nella stessa cartella delle trame. Per risolvere il problema basta rendere i percorsi delle trame relativi in 3ds Max, operazione piuttosto semplice.

1. Sulla barra degli strumenti principale scegliere **File** > **Reference** > **Asset Tracking Toggle** (File > Riferimenti > Attiva/Disattiva rilevamento asset). 

1. Nella finestra Asset Tracking (Rilevamento asset) verranno visualizzate tutte o quasi tutte le trame che sono state applicate ai materiali PBR elencati nella colonna **Maps/Shaders** (Mappe/Shader).

1. La colonna **Full Path** (Percorso completo) accanto mostra il percorso delle trame, in genere la posizione nel computer locale.

1. Infine, la colonna **Status** (Stato) indica se una determinata trama è stata individuata e applicata alla scena. Contrassegna la trama con il termine **OK**, **Found** (Trovato) o **File Missing** (File mancante). I primi due indicano che il file è stato trovato e caricato. L'ultimo significa ovviamente che il file non è stato individuato.
 
   ![Screenshot che mostra la finestra di rilevamento degli asset.](media/3dsmax/texture-paths.jpg)

Come si può notare, non tutte le trame sono incluse nella finestra Asset Tracking quando viene aperta la prima volta. Non c'è nulla di cui preoccuparsi, in quanto basta eseguire il processo di ricerca dei percorsi una o due volte per trovare in genere tutte le trame della scena. Il processo di ricerca dei percorsi è il seguente: 

1. Nella finestra Asset Tracking tenere premuto **MAIUSC** e selezionare la prima trama dell'elenco **Maps/Shaders** (Mappe/Shader) e quindi, continuando a tenere premuto **MAIUSC**, fare clic sull'ultima trama dell'elenco. Verranno selezionate tutte le trame nell'elenco. Le trame selezionate sono evidenziate in blu. Vedere lo screenshot precedente.

1. Fare clic con il pulsante destro del mouse sulla selezione e quindi scegliere **Set Path** (Imposta percorso).

1. Nella casella **Specify Asset Path** (Specifica percorso asset) selezionare il percorso locale delle trame e sostituirlo con `.\`.  Selezionare **OK**. 

    La finestra Asset Tracking verrà aggiornata come illustrano nello screenshot seguente. Questo aggiornamento potrebbe richiedere del tempo, a seconda del numero di trame presenti nella scena e delle dimensioni della scena.
![Screenshot che mostra la finestra di rilevamento degli asset aggiornata.](media/3dsmax/resolve-textures.jpg)

Si noti che la colonna **Full Path** (Percorso completo) è ora vuota. Questo significa che la scena non cerca più le trame appropriate in una posizione specifica (assoluta). Le troverà sempre purché il file con estensione max o il file FBX correlato si trovi nella stessa cartella delle trame. 

>[!NOTE]
>Può capitare che si debba ripetere questo processo un paio di volte per trovare e risolvere tutte le trame e i percorsi. Non c'è nulla di cui preoccuparsi, ripetere il processo finché non vengono trovati tutti gli asset pertinenti. In alcuni casi, specifici file non vengono trovati. A quel punto è sufficiente selezionare tutti gli asset e fare clic su **Remove Missing Paths** (Rimuovi percorsi mancanti). Vedere l'immagine precedente.

## <a name="fbx-export"></a>Esportazione in formato FBX

Ora che sono stati creati percorso relativi delle trame, è possibile procedere con l'esportazione in FBX. Anche in questo caso il processo è semplice e può essere eseguito in due modi. 

>[!TIP]
>A meno che non si voglia esportare l'intera scena, è consigliabile selezionare per l'esportazione solo gli asset necessari. Nelle scene con molte risorse l'esportazione può richiedere tempi lunghi.
>
>Se sono stati usati modificatori come Turbosmooth o Open SubDiv, è consigliabile comprimerli prima di eseguire l'esportazione, in quanto potrebbero causare problemi. Assicurarsi di salvare la scena prima di comprimerli. 

1. Nella scena selezionare gli asset da esportare. Sulla barra degli strumenti principale scegliere **File** > **Export** > **Export Selected** (File > Esporta > Esporta selezioni).

1. Nella finestra di dialogo **Select File to Export** (Seleziona file da esportare) selezionare un nome file di output. Nell'elenco **Salva con nome** selezionare **Autodesk (*.fbx)** . Viene aperta la finestra di esportazione in FBX.

  >[!IMPORTANT] 
  >Se nella scena sono state create istanze, è importante selezionare l'opzione **Preserve Instances** (Mantieni istanze) nelle impostazioni di esportazione in FBX. 

  ![Screenshot che mostra come esportare in FBX.](media/3dsmax/fbx-export.jpg)

  Tenere presente che esistono due modi per esportare il file. Se si intende condividere il FBX insieme ai relativi file delle trame in una cartella o in una directory, le impostazioni mostrate nello screenshot seguente dovrebbero funzionare correttamente. 

   Se invece si preferisce non condividere cartelle o directory di trame di grandi dimensioni insieme al file FBX, è possibile scegliere di incorporare le trame nel file FBX. Se si incorporano le trame, l'intero asset, incluse le trame, viene aggiunto a un singolo file FBX. In questo modo l'esportazione viene combinata in un singolo asset, ma il file FBX avrà dimensioni notevolmente più grandi.

   >[!IMPORTANT]
   >Se il file FBX risultante è di dimensioni maggiori di 2,4 GB, la versione minima specificata nelle impostazioni di esportazione in FBX deve essere 2016 o successiva. Vedere lo screenshot precedente. Le versioni più recenti prevedono il supporto a 64 bit e pertanto supportano file più grandi.

1. Se si vuole esportare la scena con le trame incluse, nella finestra di esportazione in FBX selezionare **Embed Media** (Incorpora file multimediali). 

1. Selezionare il resto delle impostazioni e quindi selezionare **OK**:

    ![Screenshot che mostra le impostazioni di esportazione in FBX.](media/3dsmax/fbx-settings.jpg)


   Quando si esporta in FBX usando un materiale fisico, è probabile che dopo aver selezionato **OK** nella finestra di esportazione venga visualizzato l'avviso seguente: 

   ![Screenshot che mostra l'avviso di esportazione di materiali non riuscita.](media/3dsmax/export-warnings.jpg)

   Questo avviso informa semplicemente che i materiali esportati potrebbero non essere compatibili con altri pacchetti software. Poiché il materiale fisico è compatibile con Rendering remoto di Azure, non c'è da preoccuparsi. 

1. Selezionare **OK** per completare il processo e chiudere la finestra.

## <a name="conclusion"></a>Conclusioni

In generale, questo tipo di materiale ha un aspetto più realistico perché è basato sulla fisica della luce. Crea un ulteriore effetto immersivo che fa percepire la scena come realmente esistente nel mondo reale.

## <a name="next-steps"></a>Passaggi successivi

Si è appreso come configurare materiali con illuminazione avanzata per gli oggetti di una scena e come esportare gli oggetti nel formato FBX, che è supportato da Rendering remoto di Azure. Il passaggio successivo consiste nel convertire il file FBX e visualizzarlo in Rendering remoto di Azure.

>[!div class="nextstepaction"]
>[Avvio rapido: Convertire un modello per il rendering](../../quickstarts\convert-model.md)
