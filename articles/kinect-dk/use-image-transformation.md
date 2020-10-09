---
title: Usare le trasformazioni delle immagini di Azure Kinect Sensor SDK
description: Informazioni su come usare le funzioni di trasformazione immagine di Azure Kinect Sensor SDK.
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.topic: conceptual
ms.date: 06/26/2019
keywords: Kinect, Azure, Sensor, SDK, sistema di coordinate, calibrazione, progetto, Unproject, trasformazione, RGB-d, punto cloud
ms.openlocfilehash: df7f2aa13c0e9c0241494e96e720b30f3ff1d8a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85276649"
---
# <a name="use-azure-kinect-sensor-sdk-image-transformations"></a>Usare le trasformazioni delle immagini di Azure Kinect Sensor SDK

Seguire le funzioni specifiche per usare e trasformare le immagini tra i sistemi con fotocamera coordinata in Azure Kinect DK.

## <a name="k4a_transformation-functions"></a>funzioni k4a_transformation

 Tutte le funzioni con prefisso *k4a_transformation* operano su intere immagini. Richiedono l'handle della trasformazione [k4a_transformation_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__transformation__t.html) ottenuti tramite [k4a_transformation_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga853a1a5b6d521bbdf523a69e890c4f10.html#ga853a1a5b6d521bbdf523a69e890c4f10) e non vengono allocati tramite [k4a_transformation_destroy ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7d3ecaae66f26c1a89da9042b1bc6d44.html#ga7d3ecaae66f26c1a89da9042b1bc6d44). È anche possibile fare riferimento all' [esempio di trasformazione](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/transformation) dell'SDK che illustra come usare le tre funzioni in questo argomento.

Vengono illustrate le funzioni seguenti:

* [`k4a_transformation_depth_image_to_color_camera()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514)
* [`k4a_transformation_depth_image_to_color_camera_custom()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gac00dd00e7612a86382e3d0a130f276bb.html#gac00dd00e7612a86382e3d0a130f276bb)
* [`k4a_transformation_color_image_to_depth_camera()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf3a941f07bb0185cd7a72699a648fc29.html#gaf3a941f07bb0185cd7a72699a648fc29)
* [`k4a_transformation_depth_image_to_point_cloud()`](
https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7385eb4beb9d8892e8a88cf4feb3be70.html#ga7385eb4beb9d8892e8a88cf4feb3be70)

### <a name="k4a_transformation_depth_image_to_color_camera"></a>k4a_transformation_depth_image_to_color_camera

#### <a name="overview"></a>Panoramica

 La funzione [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) trasforma la mappa di profondità dal punto di vista della fotocamera di profondità al punto di vista della fotocamera dei colori. Questa funzione è progettata per produrre immagini RGB-D così denominate, dove D rappresenta un canale immagine aggiuntivo che registra il valore di profondità. Come illustrato nella figura seguente, l'immagine del colore e l'output di [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) sembrano essere stati ricavati dallo stesso punto di vista, ovvero dal punto di vista della fotocamera dei colori.

   ![Trasformazione immagine](./media/how-to-guides/image-transformation.png)

#### <a name="implementation"></a>Implementazione

 Questa funzione di trasformazione è più complessa rispetto alla semplice chiamata di [k4a_calibration_2d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4) per ogni pixel. Si deforma una mesh triangolare dalla geometria della fotocamera di profondità alla geometria della fotocamera dei colori. La mesh triangolare viene usata per evitare la generazione di buchi nell'immagine di profondità trasformata. Un buffer Z garantisce che le occlusioni siano gestite correttamente. Per impostazione predefinita, l'accelerazione GPU è abilitata per questa funzione.

#### <a name="parameters"></a>Parametri

 I parametri di input sono l'handle di trasformazione e un'immagine di profondità. La risoluzione dell'immagine di profondità deve corrispondere all' ```depth_mode``` oggetto specificato al momento della creazione dell'handle di trasformazione. Se, ad esempio, l'handle di trasformazione è stato creato con la ```K4A_DEPTH_MODE_WFOV_UNBINNED``` modalità 1024x1024, la risoluzione dell'immagine di profondità deve essere 1024x1024 pixel. L'output è un'immagine di profondità trasformata che deve essere allocata dall'utente tramite la chiamata di [k4a_image_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef). La risoluzione dell'immagine di profondità trasformata deve corrispondere all' ```color_resolution``` oggetto specificato al momento della creazione dell'handle di trasformazione. Se, ad esempio, la risoluzione del colore è stata impostata su `K4A_COLOR_RESOLUTION_1080P` , la risoluzione dell'immagine di output deve essere di 1920x1080 pixel. Lo stride dell'immagine di output è impostato su `width * sizeof(uint16_t)` , in quanto l'immagine archivia i valori di profondità a 16 bit.

### <a name="k4a_transformation_depth_image_to_color_camera_custom"></a>k4a_transformation_depth_image_to_color_camera_custom

#### <a name="overview"></a>Panoramica

 La funzione [k4a_transformation_depth_image_to_color_camera_custom ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gac00dd00e7612a86382e3d0a130f276bb.html#gac00dd00e7612a86382e3d0a130f276bb) trasforma la mappa di profondità e un'immagine personalizzata dal punto di vista della fotocamera di profondità al punto di vista della fotocamera dei colori. Come estensione di [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514), questa funzione è progettata per produrre un'immagine personalizzata corrispondente per la quale ogni pixel corrisponde alle coordinate pixel corrispondenti della fotocamera a colori aggiuntiva all'immagine di profondità trasformata.

#### <a name="implementation"></a>Implementazione

 Questa funzione di trasformazione produce l'immagine di profondità trasformata nello stesso modo in cui [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514). Per trasformare l'immagine personalizzata, questa funzione fornisce opzioni di utilizzo dell'interpolazione lineare o dell'interpolazione vicina più vicina. L'uso dell'interpolazione lineare può creare nuovi valori nell'immagine personalizzata trasformata. Se si utilizza l'interpolazione vicina più vicina, i valori non presenti nell'immagine originale non verranno visualizzati nell'immagine di output ma comporteranno un'immagine meno uniforme. L'immagine personalizzata deve essere a canale singolo a 8 bit o a 16 bit. Per impostazione predefinita, l'accelerazione GPU è abilitata per questa funzione.

#### <a name="parameters"></a>Parametri

 I parametri di input sono l'handle di trasformazione, un'immagine di profondità, un'immagine personalizzata e il tipo di interpolazione. L'immagine di profondità e la risoluzione dell'immagine personalizzata devono corrispondere all' `depth_mode` oggetto specificato al momento della creazione dell'handle di trasformazione. Se, ad esempio, l'handle di trasformazione è stato creato con la `K4A_DEPTH_MODE_WFOV_UNBINNED` modalità 1024x1024, la risoluzione dell'immagine di profondità e dell'immagine personalizzata deve essere 1024x1024 pixel. `interpolation_type`Deve essere `K4A_TRANSFORMATION_INTERPOLATION_TYPE_LINEAR` o `K4A_TRANSFORMATION_INTERPOLATION_TYPE_NEAREST` . L'output è un'immagine di profondità trasformata e un'immagine personalizzata trasformata che devono essere allocate dall'utente tramite la chiamata di [k4a_image_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef). La risoluzione dell'immagine di profondità trasformata e dell'immagine personalizzata trasformata deve corrispondere all' `color_resolution` oggetto specificato al momento della creazione dell'handle di trasformazione. Se, ad esempio, la risoluzione del colore è stata impostata su `K4A_COLOR_RESOLUTION_1080P` , la risoluzione dell'immagine di output deve essere di 1920x1080 pixel. Lo stride dell'immagine di profondità di output è impostato su `width * sizeof(uint16_t)` , in quanto l'immagine archivia i valori di profondità a 16 bit. L'immagine personalizzata di input e l'immagine personalizzata trasformata devono essere di formato `K4A_IMAGE_FORMAT_CUSTOM8` o `K4A_IMAGE_FORMAT_CUSTOM16` . lo stride dell'immagine corrispondente deve essere impostato di conseguenza. 

### <a name="k4a_transformation_color_image_to_depth_camera"></a>k4a_transformation_color_image_to_depth_camera

#### <a name="overview"></a>Panoramica

 La funzione [k4a_transformation_color_image_to_depth_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf3a941f07bb0185cd7a72699a648fc29.html#gaf3a941f07bb0185cd7a72699a648fc29) trasforma l'immagine a colori dal punto di vista della fotocamera dei colori al punto di vista della fotocamera con profondità (vedere la figura precedente). Può essere usato per generare immagini RGB-D.

#### <a name="implementation"></a>Implementazione

 Per ogni pixel della mappa di profondità, la funzione utilizza il valore di profondità del pixel per calcolare la coordinata del sottopixel corrispondente nell'immagine del colore. Si cerca quindi il valore del colore in questa coordinata nell'immagine a colori. L'interpolazione bilineare viene eseguita nell'immagine del colore per ottenere il valore di colore alla precisione del sottopixel. Un pixel a cui non è associata una lettura di profondità viene assegnato a un valore BGRA di `[0,0,0,0]` nell'immagine di output. Per impostazione predefinita, l'accelerazione GPU è abilitata per questa funzione. Poiché questo metodo produce buchi nell'immagine del colore trasformato e non gestisce le occlusioni, è consigliabile utilizzare la funzione [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) .

#### <a name="parameters"></a>Parametri

I parametri di input sono l'handle di trasformazione, un'immagine di profondità e un'immagine a colori. Le risoluzioni delle immagini di profondità e colore devono corrispondere alla depth_mode e color_resolution specificate al momento della creazione dell'handle di trasformazione. L'output è un'immagine di colore trasformata che deve essere allocata dall'utente tramite la chiamata di [k4a_image_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef). La risoluzione dell'immagine dei colori trasformata deve corrispondere alla depth_resolution specificata al momento della creazione dell'handle di trasformazione. L'immagine di output archivia i valori a 4 8 bit che rappresentano BGRA per ogni pixel. Quindi, lo stride dell'immagine è ```width * 4 * sizeof(uint8_t)``` . L'ordine dei dati è con interfoliazione pixel, ovvero, valore blu-pixel 0, valore verde-pixel 0, valore rosso-pixel 0, valore alfa-pixel 0, valore blu-pixel 1 e così via.

### <a name="k4a_transformation_depth_image_to_point_cloud"></a>k4a_transformation_depth_image_to_point_cloud

#### <a name="overview"></a>Panoramica

La funzione [k4a_transformation_depth_image_to_point_cloud ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7385eb4beb9d8892e8a88cf4feb3be70.html#ga7385eb4beb9d8892e8a88cf4feb3be70) converte una mappa di profondità 2D eseguita da una fotocamera in un cloud di punti 3D nel sistema di coordinate della stessa fotocamera. La fotocamera può quindi essere la fotocamera con profondità o colori.

#### <a name="implementation"></a>Implementazione

 La funzione fornisce risultati equivalenti all'esecuzione di [k4a_calibration_2d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4) per ogni pixel, ma è più efficiente dal punto di vista del calcolo. Quando si chiama [k4a_transformation_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga853a1a5b6d521bbdf523a69e890c4f10.html#ga853a1a5b6d521bbdf523a69e890c4f10), viene precalcolata una cosiddetta tabella di ricerca XY che archivia i fattori di scala x e y per ogni pixel dell'immagine. Quando si chiama [k4a_transformation_depth_image_to_point_cloud ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7385eb4beb9d8892e8a88cf4feb3be70.html#ga7385eb4beb9d8892e8a88cf4feb3be70), si ottiene una coordinata x 3D del pixel moltiplicando il fattore di scala x del pixel con la coordinata Z del pixel. Analogamente, la coordinata Y 3D viene calcolata in base alla moltiplicazione con il fattore di scala y. L' [esempio di Cloud Point veloce](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/fastpointcloud) dell'SDK illustra come viene calcolata la tabella XY. Gli utenti possono seguire il codice di esempio per implementare la propria versione di questa funzione, ad esempio, per velocizzare la pipeline della GPU.

#### <a name="parameters"></a>Parametri

 I parametri di input sono l'handle di trasformazione, un identificatore della fotocamera e un'immagine di profondità. Se l'identificatore della fotocamera è impostato su Depth, la risoluzione dell'immagine di profondità deve corrispondere alla depth_mode specificata al momento della creazione dell'handle di trasformazione. In caso contrario, se l'identificatore è impostato sulla fotocamera dei colori, la risoluzione deve corrispondere alla risoluzione del color_resolution scelto. Il parametro di output è un'immagine XYZ che deve essere allocata dall'utente tramite la chiamata di [k4a_image_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef). La risoluzione dell'immagine XYZ deve corrispondere alla risoluzione della mappa di profondità di input. Vengono archiviati tre valori di coordinate a 16 bit con segno in millimetri per ogni pixel. Lo stride dell'immagine XYZ viene quindi impostato su `width * 3 * sizeof(int16_t)` . L'ordine dei dati è un pixel con interfoliazione, ovvero X-coordinata-pixel 0, coordinata Y-pixel 0, Z-coordinata-pixel 0, coordinata X-pixel 1 e così via. Se non è possibile convertire un pixel in 3D, la funzione assegna i valori `[0,0,0]` al pixel.

## <a name="samples"></a>Esempi

[Esempio di trasformazione](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/transformation)

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come usare le funzioni di trasformazione immagine di Azure Kinect Sensor SDK, è anche possibile ottenere informazioni su

>[!div class="nextstepaction"]
>[Funzioni di calibrazione dell'SDK del sensore Kinect di Azure](use-calibration-functions.md)

È anche possibile esaminare

[Sistemi di coordinate](coordinate-systems.md)
