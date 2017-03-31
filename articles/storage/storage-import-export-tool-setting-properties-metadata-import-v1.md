---
title: "Impostazione delle proprietà e dei metadati usando Importazione/Esportazione di Azure | Documentazione Microsoft"
description: "Informazioni su come specificare le proprietà e i metadati da impostare nei BLOB di destinazione quando si esegue lo strumento Importazione/Esportazione per preparare le unità."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: e8541695-bcfb-4bf0-84d9-72c9de32a0a8
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 8de848b1192ff1c10e0375053c4e03f18c06184e
ms.openlocfilehash: 8e1152a03eee01244dd469586848c396a7f689cc
ms.lasthandoff: 02/16/2017


---

# <a name="setting-properties-and-metadata-during-the-import-process"></a>Impostazione di proprietà e metadati durante il processo di importazione
Quando si esegue lo strumento Importazione/Esportazione di Microsoft Azure per preparare le unità, è possibile specificare le proprietà e i metadati da impostare nei BLOB di destinazione. A tale scopo, seguire questa procedura:  
  
1.  Per impostare le proprietà dei BLOB, creare nel computer locale un file di testo che specifichi i nomi e i valori delle proprietà.  
  
2.  Per impostare i metadati dei BLOB, creare nel computer locale un file di testo che specifichi i nomi e i valori dei metadati.  
  
3.  Passare il percorso completo di uno di questi file o di entrambi allo strumento Importazione/Esportazione di Azure durante l'operazione `PrepImport`.  
  
> [!NOTE]
>  Quando si specifica un file di proprietà o di metadati durante una sessione di copia, tali proprietà o metadati vengono impostati per ogni BLOB importato durante tale sessione di copia. Per specificare un set diverso di proprietà o metadati per alcuni dei BLOB da importare, sarà necessario creare una sessione di copia separata con file di proprietà o metadati diversi.  
  
## <a name="specify-blob-properties-in-a-text-file"></a>Specificare le proprietà dei BLOB in un file di testo  
Per specificare le proprietà dei BLOB, creare un file di testo locale e includere un codice XML che specifichi i nomi delle proprietà come elementi e i valori delle proprietà come valori. Ecco un esempio che specifica alcuni valori delle proprietà:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
    <Content-Type>application/octet-stream</Content-Type>  
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>  
    <Cache-Control>no-cache</Cache-Control>  
</Properties>  
```
  
Salvare il file in un percorso locale, ad esempio `C:\WAImportExport\ImportProperties.txt`.  
  
## <a name="specify-blob-metadata-in-a-text-file"></a>Specificare i metadati dei BLOB in un file di testo  
Allo stesso modo, per specificare i metadati dei BLOB, creare un file di testo locale che specifichi i nomi dei metadati come elementi e i valori dei metadati come valori. Ecco un esempio che specifica alcuni valori dei metadati:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
    <UploadMethod>Windows Azure Import/Export Service</UploadMethod>  
    <DataSetName>SampleData</DataSetName>  
    <CreationDate>10/1/2013</CreationDate>  
</Metadata>  
```
  
Salvare il file in un percorso locale, ad esempio `C:\WAImportExport\ImportMetadata.txt`.  
  
## <a name="create-a-copy-session-including-the-properties-or-metadata-files"></a>Creare una sessione di copia che includa i file delle proprietà o dei metadati  
Quando si esegue lo strumento Importazione/Esportazione di Azure per preparare il processo di importazione, specificare il file delle proprietà nella riga di comando tramite il parametro `PropertyFile`. Specificare il file dei metadati nella riga di comando tramite il parametro `/MetadataFile`. Ecco un esempio che consente di specificare entrambi i file:  
  
```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:BlueRayIso /srcfile:K:\Temp\BlueRay.ISO /dstblob:favorite/BlueRay.ISO /MetadataFile:c:\WAImportExport\SampleMetadata.txt /PropertyFile:c:\WAImportExport\SampleProperties.txt  
```
  
## <a name="see-also"></a>Vedere anche  
[Import-Export Service Metadata and Properties File format](storage-import-export-file-format-metadata-and-properties.md) (Formato dei file di proprietà e metadati del servizio Importazione/Esportazione)

