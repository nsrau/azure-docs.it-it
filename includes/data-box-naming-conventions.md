---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: d965b89659a9e3dc01035221a729f094c336eb5b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "66244575"
---
| Entità                                       | Convenzioni   |
|----------------------------------------------|-----------------------------------------------------|
| Nomi di contenitori BLOB in blocchi e BLOB di pagine | Deve essere un nome DNS valido compreso tra 3 e 63 caratteri. <br>  Deve iniziare con una lettera o un numero. <br> Può contenere solo lettere minuscole, numeri e trattini (-). <br> Ogni trattino (-) deve essere immediatamente preceduto e seguito da una lettera o un numero. <br> Nei nomi non sono consentiti trattini consecutivi. |
| Nomi delle condivisioni per i file di Azure                  | Vedere sopra.                                          |
| Nomi dei file e delle directory per i file di Azure     |<li> Mantengono le maiuscole/minuscole, non fanno distinzione tra maiuscole e minuscole e non devono superare i 255 caratteri. </li><li> Non possono terminare con la barra (/). </li><li>Se inserita, verrà automaticamente rimossa. </li><li> Non sono consentiti i caratteri seguenti:<code>" \\ / : \| < > * ?</code></li><li> I caratteri URL riservati devono essere preceduti da una sequenza di escape. </li><li> Non sono consentiti caratteri di percorso URL non validi. I punti di \\codice come uE000 non sono caratteri Unicode validi. Anche alcuni caratteri ASCII o Unicode, ad esempio i caratteri di controllo ( \\da 0x00 a 0x1F, u0081 e così via), non sono consentiti. Per le regole che controllano le stringhe Unicode in HTTP/1.1, vedere RFC 2616, sezione 2.2: Basic Rules e RFC 3987. </li><li> I nomi file seguenti non sono consentiti: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK $, carattere punto (.) e due caratteri punto (..).</li>|
| Nomi di BLOB per BLOB in blocchi e BLOB di pagine      | </li><li>I nomi di BLOB fanno distinzione tra maiuscole e minuscole e possono contenere una qualsiasi combinazione di caratteri. </li><li>La lunghezza di un nome di BLOB deve essere compresa tra 1 e 1.024 caratteri. </li><li>I caratteri URL riservati devono essere preceduti da una sequenza di escape. </li><li>Il numero di segmenti del percorso che includono il nome BLOB non può essere superiore a 254. Un segmento di percorso è la stringa tra caratteri di delimitatore consecutivi, ad esempio, la barra rovesciata '/', che corrisponde al nome di una directory virtuale.</li> |
