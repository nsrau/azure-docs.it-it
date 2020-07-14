---
title: Configurazione di materiali basati su PBR (Physically Based Rendering) in 3DSMax
description: Questa esercitazione spiega come configurare materiali basati su PBR (Physically Based Rendering) in 3DSMax ed esportarli in formato FBX.
author: muxanickms
ms.author: misams
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: df4be8963c93199f9fad23ab3f709f691e1da768
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85857552"
---
# <a name="tutorial-set-up-physically-based-rendering-materials-in-3d-studio-max"></a>Esercitazione: Configurare materiali basati su PBR (Physically Based Rendering) in 3D Studio Max

## <a name="overview"></a>Panoramica
In questa esercitazione si apprenderà come:

>[!div class="checklist"]
>
> * Assegnare materiali con illuminazione avanzata agli oggetti nella scena.
> * Gestire la creazione di istanze di oggetti e materiali.
> * Esportare una scena in formato FBX e selezionare opzioni importanti.

La creazione di [materiali basati su PBR (Physically Based Rendering)](../../overview/features/pbr-materials.md) in 3D Studio Max (3DSMax) è un'attività relativamente semplice. È simile sotto molti aspetti alla configurazione di materiali basati su PBR in altre app per la creazione di contenuti come Maya. Questa esercitazione è una guida alla configurazione di una funzione shader PBR di base e all'esportazione in formato FBX per i progetti di Rendering remoto di Azure.

La scena di esempio dell'esercitazione contiene una serie di oggetti poligono a cui sono assegnati materiali diversi, ossia legno, metallo, metallo verniciato, plastica e gomma. In generale, ogni materiale contiene tutte o la maggior parte delle trame seguenti:

* **Albedo**, che è la mappa colori del materiale, denominata anche **Diffuse** o **BaseColor**.
* **Metalness**, che determina se un materiale è metallico e quali parti sono metalliche. 
* **Roughness**, che determina quanto è ruvida o liscia una superficie,
determinando di conseguenza la nitidezza o la sfocatura dei riflessi e delle aree di massima luce sulla superficie stessa.
* **Normal**, che aggiunge dettagli a una superficie senza bisogno di aggiungere altri poligoni. Esempi di dettaglio possono essere erosioni e ammaccature su una superficie metallica o venature su una superficie in legno.
* **Ambient Occlusion**, che viene usata per aggiungere ombreggiature sfumate e ombre da contatto a un modello. Si tratta di una mappa in scala di grigi che indica quali aree di un modello sono totalmente illuminate (bianche) o totalmente in ombra (nere).

## <a name="prepare-the-scene"></a>Preparare la scena
Il processo di configurazione di un materiale PBR in **3D Studio Max** è descritto di seguito.

Per iniziare, come si vedrà nella scena di esempio, sono stati creati alcuni oggetti scatola, ognuno dei quali rappresenta un tipo di materiale diverso:

>[!TIP]
>Prima di iniziare a creare asset per Rendering remoto di Azure, vale la pena di sottolineare che questo servizio usa il **metro** come unità di misura.  
>È quindi consigliabile impostare le **unità di sistema** della scena sul **metro**. Inoltre, ai fini dell'esportazione è consigliabile impostare le unità di sistema sul metro nelle impostazioni di esportazione FBX.

La figura seguente illustra la procedura per impostare le unità di sistema sul metro in 3D Studio Max. Nel menu principale passare a **Customize** > **Units Setup** > **System Units Setup** (Personalizza > Configurazione unità > Configurazione unità di sistema) e quindi nell'elenco a discesa **System Units Scale** (Scala unità di sistema) selezionare **Meters** (Metri). 
![Unità di sistema](media/3dsmax/system-units.jpg)

Un volta impostate le unità di sistema sul metro, è possibile iniziare a creare i modelli. Nella scena di esempio vengono creati diversi oggetti scatola, ognuno dei quali rappresenta un tipo di materiale diverso, ad esempio metallo, gomma, plastica e così via. 

>[!TIP]
>È consigliabile assegnare agli asset nomi appropriati man mano che si creano. In questo modo sarà più facile trovarli successivamente se la scena contiene molti oggetti.

![rename-objects](media/3dsmax/rename-objects.jpg)

## <a name="assign-materials"></a>Assegnare i materiali

Dopo aver creato alcuni oggetti nella scena, in questo caso alcuni cubi, è possibile iniziare la configurazione del materiale PBR:

* Nella barra degli strumenti principale fare clic sull'icona **Material Editor** (Editor materiali), come illustrato nella figura seguente. È anche possibile premere **M** sulla tastiera per aprire l'editor. L'editor dei materiali ha due modalità che possono essere selezionate nell'elenco a discesa **Modes** (Modalità): **Compact Material Editor** e **Slate Material Editor**. Poiché questa scena è relativamente semplice, verrà usata la **modalità compatta**.

* All'interno dell'editor dei materiali si vedranno diverse sfere, che rappresentano i materiali di questa esercitazione. Si assegnerà uno di questi materiali a ogni oggetto scatola della scena. A questo scopo occorre prima selezionare uno degli oggetti nel riquadro di visualizzazione principale. Fare quindi clic sulla prima sfera nella finestra dell'editor dei materiali. Una volta assegnato a un oggetto, il materiale selezionato verrà evidenziato come illustrato nell'immagine seguente.

* Fare clic sul pulsante **Assign Material to Selection** (Assegna materiale alla selezione) come illustrato di seguito. Il materiale selezionato è stato ora assegnato all'oggetto selezionato.
![assign-material](media/3dsmax/assign-material.jpg)

Nell'editor dei materiali è possibile selezionare un'ampia varietà di materiali, a seconda del caso d'uso. In genere, il tipo di materiale è impostato su **Standard** per impostazione predefinita. Si tratta di un materiale di base non adatto per la configurazione PBR, quindi è necessario cambiare il tipo di materiale in un materiale PBR. Il materiale preferito di **3DSMax** per i progetti di Rendering remoto di Azure è **Physical Material** (Materiale fisico).

* Nell'editor dei materiali fare clic sulla scheda **Standard** e quindi nel browser di materiali/mappe visualizzato selezionare **Physical Material**. Questa azione converte il materiale **standard** assegnato in **materiale fisico** PBR.
![physical-material](media/3dsmax/physical-material.jpg)

* Nell'editor dei materiali vengono ora visualizzate le proprietà del materiale fisico (immagine seguente) ed è possibile iniziare ad assegnare trame all'asset.
![textures-list](media/3dsmax/textures-list.jpg)

Come si può notare dall'immagine qui sopra, è disponibile un'ampia gamma di mappe e trame che è possibile aggiungere al materiale. Ai fini di questa esercitazione, tuttavia, si useranno solo cinque slot di trame nel materiale.

>[!TIP]
>È consigliabile assegnare ai materiali nomi appropriati, come illustrato nell'immagine precedente.

A questo punto è possibile iniziare a prendere in considerazione l'assegnazione di trame al materiale. La modalità di generazione delle trame può variare in base alle preferenze o all'utilizzo. Ad esempio, è possibile usare trame di rivestimento che possono essere applicate a qualsiasi asset oppure applicare a parti specifiche di un progetto o di un asset un set personalizzato di trame. Si possono usare trame di rivestimento generiche disponibili online o creare trame personalizzate in app come **Photoshop**, **Quixel Suite**, **Substance Suite** e altre. 

Prima di iniziare ad assegnare le trame, è necessario prendere in considerazione le coordinate delle trame degli asset (UVW). Una procedura consigliata, quando si applicano trame a un modello, è assicurarsi che il wrapping del modello sia stato rimosso (le trame non vengono visualizzate correttamente senza una corretta rimozione del wrapping delle coordinate UV), e questo è particolarmente importante ai fini di questa esercitazione se si intende usare una mappa **Ambient Occlusion** sul modello. A differenza dello **shader Stingray** in **Maya**, il materiale **Physical Material** in **3DSMax** non ha una trama **Ambient Occlusion** dedicata. Verrà quindi applicata la mappa Ambient Occlusion (AO) a un altro slot e, per consentirne l'uso separatamente dalle altre trame (ad esempio le trame di rivestimento), le verrà assegnato un canale di mappa UVW autonomo. 

Per iniziare, si assegnerà un modificatore **Unwrap UVW** al modello come illustrato di seguito:

* Nell'editor delle proprietà degli oggetti selezionati fare clic sull'elenco dei modificatori e quindi nell'elenco a discesa che si aprirà scorrere verso il basso e selezionare Unwrap UVW. Verrà applicato un modificatore Unwrap UVW all'asset.
![unwrap-modifier](media/3dsmax/unwrap-modifier.jpg)

* Il canale mappe è impostato su 1. È in questo canale che viene in genere eseguita la rimozione del wrapping. In questo caso, il wrapping è stato rimosso dall'oggetto senza alcuna sovrapposizione delle coordinate delle trame (UV).
![unwrapped-uvw](media/3dsmax/unwrapped-uvw.jpg)

Il passaggio successivo consiste nel creare un secondo canale mappe UV.

* Chiudere l'editor UV, se è aperto, e nella sezione Channel (Canale) del menu **Edit UVs** (Modifica UV) impostare il numero del canale su 2. Il canale mappe 2 è il canale previsto per le mappe Ambient Occlusion. 

* Nella finestra di dialogo **Channel Change Warning** (Avviso cambio canale) è possibile selezionare **Move** (Sposta) per spostare le coordinate UV esistenti dal canale 1 al nuovo canale 2 oppure **Abandon** (Abbandona) per abbandonare le coordinate UV esistenti che creeranno automaticamente un nuovo **UV Unwrap**. Selezionare **Abandon** solo se si intende creare un nuovo **UV unwrap** per la mappa Ambient Occlusion diverso dalle coordinate UV nel canale mappe 1 (ad esempio se si vogliono usare le trame di rivestimento nel canale 1). Ai fini di questa esercitazione si selezionerà **Move** per spostare le coordinate UV dal canale 1 al canale 2, in quanto non è necessario modificare il nuovo canale UV.

>[!NOTE]
>Anche se l'UV Unwrap è stato copiato (scegliendo **Move**) dal canale mappe 1 al canale mappe 2, è possibile apportare tutte le modifiche necessarie alle coordinate UV del nuovo canale senza alterare il canale mappe originale.

![channel-change](media/3dsmax/channel-change.jpg)

Una volta creato il nuovo canale mappe, è possibile tornare al materiale fisico nell'editor dei materiali e iniziare ad aggiungervi le trame. Si aggiungerà prima di tutto la mappa Ambient Occlusion (**AO**), in quanto occorre eseguire un ulteriore passaggio per consentirne il corretto funzionamento. Dopo aver collegato la mappa AO al materiale, è necessario indicarle come usare il canale mappe 2.

* Come già accennato, non esiste uno slot dedicato per le mappe AO in **3DSMax Physical Material**. Si applicherà quindi la mappa AO allo slot **Diffuse Roughness**.

* Nell'elenco **Generic Maps** (Mappe generiche) del materiale fisico fare clic su **No Map** (Nessuna mappa) per lo slot **Diffuse Roughness** (Ruvidità diffusa) e caricare la mappa AO.

* Nelle proprietà delle trame AO si noterà che il canale mappe è impostato su **1** per impostazione predefinita. Cambiare questo valore in **2**. Questa azione completa la procedura necessaria per aggiungere la mappa Ambient Occlusion.

>[!IMPORTANT]
>È un passaggio importante, specialmente se le coordinate UV nel canale 2 sono diverse rispetto a quelle del canale 1, in quanto la mappa AO non eseguirà il mapping correttamente se è selezionato il canale errato.

![Assegnare la mappa AO](media/3dsmax/assign-ao-map.jpg)

A questo punto occorre assegnare la mappa Normal al materiale PBR. Questa azione presenta alcune differenze rispetto a **Maya** in quanto la mappa Normal non viene applicata direttamente allo slot delle mappe Bump (non esiste uno slot per la mappa Normal in **3DSMax Physical Material**), ma viene invece aggiunta a un modificatore di mappa Normal che a sua volta viene inserito nello slot **normals**.

* Nella sezione **Special Maps** (Mappe speciali) delle proprietà del materiale fisico (nell'editor dei materiali) fare clic su **No Map** (Nessuna mappa) per lo slot **Bump Map** (Mappa Bump). 

* Nel browser di materiali/mappe individuare e fare clic su **Normal Bump**. Verrà aggiunto un modificatore **Normal Bump** al materiale.

* Nel modificatore **Normal Bump** fare clic su **No Map** (Nessuna mappa) per **Normal**, quindi individuare e caricare la mappa Normal.

* Verificare che il metodo sia impostato su **Tangent** (dovrebbe essere l'impostazione predefinita) e se necessario attivare **Flip Green (Y)** .

![normal-bump](media/3dsmax/normal-bump.jpg)
![load-normal-map](media/3dsmax/load-normal-map.jpg)

Una volta assegnata correttamente la mappa Normal, è possibile procedere con l'assegnazione delle trame rimanenti per completare la configurazione del materiale fisico. È un processo semplice, senza impostazioni speciali da considerare. L'immagine seguente mostra il set completo di trame assegnate al materiale: ![all-textures](media/3dsmax/all-textures.jpg)

Una volta creati e configurati i materiali PBR, è opportuno considerare la creazione di istanze degli oggetti nella scena. La creazione di istanze di oggetti simili nella scena, ad esempio dadi, bulloni e rondelle di viti (essenzialmente, tutti gli oggetti che sono uguali) può contribuire a ridurre notevolmente le dimensioni dei file. A ogni istanza di un oggetto master è possibile assegnare una scala, una rotazione e un set trasformazioni specifiche, in modo da poterla inserire nella scena in base alle esigenze. Il processo di **creazione di istanze** in **3D Studio Max** è semplice.

* Nel riquadro di visualizzazione principale selezionare l'oggetto o gli oggetti da esportare.

* Tenere premuto **MAIUSC** e trascinare gli asset verso l'alto usando lo strumento di trasformazione (spostamento). 

* Nella finestra di dialogo **Clone Options** (Opzioni di clonazione) impostare **Object** (Oggetto) su **Instance** (Istanza) e fare clic su **OK**. 
![instance-object](media/3dsmax/instance-object.jpg)

Questa azione creerà un'istanza dell'oggetto che può essere spostata, ruotata o ridimensionata indipendentemente dall'oggetto padre e da altre istanze di tale oggetto.

>[!IMPORTANT]
>Tuttavia, le eventuali modifiche apportate a un'istanza in modalità oggetto secondario verranno trasmesse a tutte le istanze dell'oggetto, pertanto se si usano istanze di componenti di oggetti, come vertici o facce di poligoni, assicurarsi innanzitutto di voler effettivamente applicare le modifiche a tutte queste istanze. Tenere presente che qualsiasi oggetto di cui sono state create istanze può essere reso un oggetto univoco in qualsiasi momento. 

>[!TIP]
>La procedura consigliata per la creazione di istanze nella scena consiste nel crearle man mano che si procede, in quanto la sostituzione di **copie** con istanze degli oggetti in un secondo momento è estremamente difficile. 

Un aspetto finale da considerare prima di passare al processo di esportazione è il modo in cui è possibile creare un pacchetto della scena o dell'asset per la condivisione. Idealmente, se si passa l'asset a un cliente o a un membro del team, si vuole che sia in grado di aprirlo e visualizzarlo nel modo corretto con il minimo sforzo. È quindi importante che i percorsi delle trame degli asset rimangano relativi al file della scena. Se questi percorsi puntano a un'unità locale o a un percorso assoluto, non verranno caricati nella scena se aperti in un altro computer, nemmeno se il file con estensione **max** si trova nella stessa cartella delle trame. Per risolvere il problema basta rendere i percorsi delle trame relativi in 3D Studio Max, operazione piuttosto semplice.

* Nella barra degli strumenti principale passare a **File** > **Reference** > **Asset Tracking Toggle** (File > Riferimenti > Attiva/Disattiva rilevamento asset). 

* Si aprirà un browser di rilevamento degli asset in cui sono visualizzate tutte o quasi tutte le trame che sono state applicate ai materiali PBR elencati nella colonna **Maps/Shaders** (Mappe/Shader).

* La colonna **Full Path** (Percorso completo) accanto mostra il percorso file della posizione delle trame, in genere la posizione nel computer locale.

* Infine, la colonna **Status** (Stato) indica se una determinata trama è stata o meno individuata e applicata alla scena e contrassegna questa trama con il termine **OK**, **Found** (Trovata) o **File Missing** (File mancante). I primi due termini indicano che il file è stato trovato e caricato, mentre l'ultimo indica ovviamente che il file non è stato trovato.
![texture-paths](media/3dsmax/texture-paths.jpg)

Si può notare che non tutte le trame sono elencate nel browser di rilevamento degli asset quando lo si apre la prima volta. Non c'è nulla di cui preoccuparsi, in quanto basta eseguire il processo di ricerca dei percorsi una o due volte per trovare in genere tutte le trame della scena. Il processo di ricerca dei percorsi è il seguente: 

* Nella finestra di rilevamento degli asset premere **MAIUSC**+**clic** sulla prima trama dell'elenco **Maps/Shaders** (Mappe/Shader) e quindi, continuando a tenere premuto MAIUSC, fare clic sull'ultima trama dell'elenco. Verranno selezionate tutte le trame nell'elenco. Le trame selezionate saranno ora evidenziate in blu (vedere l'immagine riportata sopra).

* Fare clic con il pulsante destro del mouse sulla selezione e scegliere **Set Path** (Imposta percorso) dal menu popup visualizzato.

* Nella finestra **Specify Asset Path** (Specifica percorso asset) selezionare il percorso locale delle trame visualizzate e sostituirlo con `.\`, quindi fare clic su **OK**. 

* Dopo un periodo di tempo (che dipende dal numero di trame nella scena e dalle dimensioni della scena), la finestra di rilevamento asset dovrebbe visualizzare quanto segue (vedere l'immagine).
![resolve-textures](media/3dsmax/resolve-textures.jpg)

Si noti che la colonna **Full Path** (Percorso completo) è ora vuota. Significa che la scena non sta più cercando le trame pertinenti in un percorso specifico (assoluto), ma le troverà sempre purché il file con estensione max o il file FBX correlato si trovi nella stessa cartella delle trame. 

>[!NOTE]
>Può capitare che si debba ripetere questo processo un paio di volte per trovare e risolvere tutte le trame e i percorsi. Non c'è niente di cui preoccuparsi, basta ripetere il processo fino a quando non vengono considerati tutti gli asset pertinenti. Può anche succedere che alcuni file non debbano più essere trovati. In questo caso è sufficiente selezionare tutti gli asset e fare clic su **Remove Missing Paths** (Rimuovi percorsi mancanti), come illustrato nell'immagine sopra.

## <a name="fbx-export"></a>Esportazione in formato FBX

Una volta completato il rilevamento degli asset, è possibile procedere con l'esportazione in formato FBX. Anche in questo caso il processo è semplice e può essere eseguito in due modi. 

>[!TIP]
>A meno che non si voglia esportare l'intera scena, è consigliabile selezionare per l'esportazione solo gli asset necessari. In scene particolarmente piene di risorse l'esportazione può richiedere molto tempo, quindi è opportuno esportare solo quelle che servono.
>
>Se sono stati usati modificatori come **Turbosmooth** o **Open SubDiv** è consigliabile comprimerli prima di eseguire l'esportazione, in quanto potrebbero causare problemi. Salvare sempre la scena prima di procedere. 

* Nella scena selezionare gli asset da esportare, quindi nella barra degli strumenti principale passare a **File** > **Export** > **Export Selected** (File > Esporta > Esporta selezionati).

* Nella finestra di dialogo **Select File to Export** (Seleziona file da esportare) digitare o selezionare il nome del file di output, quindi nelle opzioni **Save as Type** (Salva come) selezionare **Autodesk (*.fbx)** . Si aprirà il menu di esportazione in formato FBX. 

* Se sono state create istanze nella scena, è importante che l'opzione **Preserve Instances** (Mantieni istanze) sia attivata nelle impostazioni di esportazione FBX. 
![fbx-export](media/3dsmax/fbx-export.jpg)

Prima si è accennato a due modi per esportare il file. Se, quando si esegue l'esportazione, l'intenzione è quella di condividere il file FBX insieme ai relativi file di trame in una cartella o in una directory, dovrebbero essere valide anche le impostazioni visualizzate nell'immagine seguente. Dopo avere selezionato le impostazioni, fare clic su **OK**.
![fbx-settings](media/3dsmax/fbx-settings.jpg)

Se invece si preferisce non condividere cartelle o directory di trame di grandi dimensioni insieme al file FBX, è possibile scegliere **Embed** per incorporare le trame nel file FBX. Questo significa che l'intero asset, incluse le trame, verrà aggiunto a un singolo FBX. Tenere presente, però, che poiché riunisce tutta l'esportazione in un singolo asset, il file FBX sarà notevolmente più grande.

>[!IMPORTANT]
>Se il file FBX risultante è superiore a 2,4 GB, la versione minima delle impostazioni di esportazione FBX (vedere sopra) deve essere la 2016 o successiva. Le versioni più recenti sono infatti dotate del supporto a 64 bit e pertanto supportano file più grandi.

* Nelle impostazioni di esportazione FBX attivare **Embed Media (Incorpora file multimediali), quindi fare clic su **OK** per eseguire l'esportazione con le trame incluse. 

Quando si esegue l'esportazione in formato FBX con l'opzione Physical Material, verrà probabilmente visualizzato il messaggio popup di avviso seguente dopo aver scelto OK nella finestra di dialogo di esportazione: ![export-warnings](media/3dsmax/export-warnings.jpg)

Questo avviso informa semplicemente l'utente che i materiali esportati potrebbero non essere compatibili con altri pacchetti software. Poiché il materiale fisico è compatibile con Rendering remoto di Azure, non c'è da preoccuparsi. Fare semplicemente clic su **OK** per completare il processo e chiudere la finestra.

## <a name="conclusion"></a>Conclusioni

In generale, questo tipo di materiale ha un aspetto più realistico perché è basato sulla fisica della luce. Crea un ulteriore effetto immersivo che fa percepire la scena come realmente esistente nel mondo reale.

## <a name="next-steps"></a>Passaggi successivi

Si è appreso come configurare materiali con illuminazione avanzata per gli oggetti di una scena e come esportare gli oggetti nel formato FBX, che è supportato da Rendering remoto di Azure. Il passaggio successivo consiste nel convertire il file FBX e visualizzarlo in Rendering remoto di Azure.

>[!div class="nextstepaction"]
>[Avvio rapido: Convertire un modello per il rendering](../../quickstarts\convert-model.md)