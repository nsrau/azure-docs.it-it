Quando si crea un gateway di rete virtuale è necessario specificare il codice SKU del gateway da usare. Quando si seleziona uno SKU superiore, al gateway vengono allocati un maggior numero di CPU e una larghezza di banda superiore, di conseguenza il gateway può supportare una velocità effettiva di rete più elevata per la rete virtuale.

Il gateway VPN può usare i codici SKU seguenti:

* Basic
* Standard
* HighPerformance

Nella scelta di uno SKU, considerare le limitazioni seguenti:

* Se si vuole usare un tipo di VPN basato su criteri, è necessario usare lo SKU di livello Basic del gateway. Le VPN basate su criteri, precedentemente denominate routing statico, non sono supportate negli altri SKU.
* BGP non è supportato nello SKU di livello Basic.
* Le configurazioni con coesistenza di gateway VPN ed ExpressRoute non sono supportate nello SKU di livello Basic.

<!--HONumber=Oct16_HO2-->


