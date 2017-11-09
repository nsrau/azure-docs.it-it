| Risorsa | Basic | Standard | Premium |
|---|---|---|---|---|
| Archiviazione | 10 GiB | 100 GiB| 500 GiB |
| Operazioni di lettura al minuto<sup>1, 2</sup> | 1.000 | 300.000 | 10.000.000 |
| Operazioni di scrittura al minuto<sup>1, 3</sup> | 100 | 500 | 2.000 |
| MBps di larghezza di banda per il download<sup>1</sup> | 30 | 60 | 100 |
| MBps di larghezza di banda per l'upload<sup>1</sup> | 10 | 20 | 50 |
| Webhook | 2 | 10 | 100 |
| Replica geografica | N/D | N/D | [Supportata *(anteprima)*](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication) |

<sup>1</sup> I dati delle *operazioni di lettura*, *operazioni di scrittura* e *larghezza di banda* sono stime minime. Registro contenitori di Azure migliora le prestazioni in base alle necessità d'uso.

<sup>2</sup> Il [Pull Docker](https://docs.docker.com/registry/spec/api/#pulling-an-image) si traduce in operazioni di lettura multiple in base al numero di livelli dell'immagine e al recupero del manifesto.

<sup>3</sup> Il [push di Docker](https://docs.docker.com/registry/spec/api/#pushing-an-image) si traduce in operazioni di scrittura multiple, in base al numero di livelli da inserire. Un `docker push` include *operazioni di lettura* per recuperare il manifesto di un'immagine esistente.