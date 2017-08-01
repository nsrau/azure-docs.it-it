Il processo genera un file di output JSON che contiene i metadati sui volti rilevati e monitorati. I metadati includono coordinate che indicano la posizione dei volti e un numero di ID volto che indica il monitoraggio della persona specifica. I codici ID del volto sono soggetti a ripristino quando le riprese non sono frontali o sono sovrapposte nel fotogramma, causando l'assegnazione di diversi ID alla stessa persona.

L'output JSON include gli attributi seguenti:

| Elemento | Descrizione |
| --- | --- |
| version |Indica la versione dell'API Video. |
| index | Solo per Azure Media Redactor. Definisce l'indice dei fotogrammi dell'evento corrente. |
| timescale |"Scatti" al secondo del video. |
| offset |Differenza di orario dei timestamp. Nella versione 1.0 delle API Video, questo valore è sempre 0. Negli scenari futuri supportati questo valore potrebbe cambiare. |
| framerate |Fotogrammi al secondo del video. |
| fragments |I metadati sono suddivisi in segmenti diversi, detti frammenti. Ogni frammento contiene un inizio, una durata, un numero di intervallo e uno o più eventi. |
| start |L'ora di inizio del primo evento in "scatti". |
| duration |La lunghezza del frammento in "scatti". |
| interval |L'intervallo di ogni voce di evento all'interno del frammento in "scatti". |
| eventi |Ogni evento contiene i volti rilevati e monitorati nel periodo specificato. È una matrice di eventi. La matrice esterna rappresenta un intervallo di tempo. La matrice interna è costituita da 0 o più eventi che si sono verificati in un determinato momento. Le parentesi quadre vuote [] indicano che non sono stati rilevati volti. |
| id |L'ID del volto monitorato. Questo numero potrebbe cambiare inavvertitamente se un volto non viene rilevato. Una determinata persona dovrebbe avere lo stesso ID in tutto il video, ma non è possibile garantire sempre lo stesso ID a causa delle limitazioni nell'algoritmo di rilevamento (occlusione e così via) |
| x, y |Le coordinate X e Y in alto a sinistra del riquadro del contorno del volto in una scala normalizzata da 0,0 a 1,0. <br/>Le coordinate X e Y si riferiscono sempre all'orientamento orizzontale, pertanto se il video è in formato verticale o se è capovolto, nel caso di iOS, sarà necessario trasporre le coordinate di conseguenza. |
| width, height |La larghezza e altezza del riquadro del contorno del volto in una scala normalizzata da 0,0 a 1,0. |
| facesDetected |Questo valore viene riportato alla fine dei risultati del file JSON e riepiloga il numero di volti rilevati dall'algoritmo durante il video. Poiché gli ID possono essere reimpostati inavvertitamente se un volto non viene rilevato, ad esempio quando il volto esce dall'inquadratura o la persona si volta, questo numero potrebbe non corrispondere sempre al vero numero di volti del video. |

