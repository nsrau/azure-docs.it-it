<properties title="Azure Machine Learning Sample: Color quantization using K-Means clustering" pageTitle="Esempio di Machine Learning: Quantizzazione di colori tramite clustering K-Means | Azure" description="A sample Azure Machine Learning experiment that evaluates using different K-Means clustering values for quantizing a color image." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Esempio di Machine Learning di Azure: Quantizzazione di colori tramite clustering K-Means

>[AZURE.NOTE]
>L'[esperimento di esempio] e il [set di dati di esempio] associati a questo modello sono disponibili in ML Studio. Per informazioni dettagliate, vedere di seguito.
[Esperimento di esempio]: #sample-experiment
[Set di dati di esempio]: #sample-dataset


##Descrizione del problema

[La quantizzazione di colori](http://en.wikipedia.org/wiki/Color_quantization "Color quantization") è il processo di riduzione del numero di colori distinti in un'immagine, ottenendone quindi la compressione. In genere si intende conservare per quanto possibile l'aspetto dei colori dell'immagine, riducendo comunque il numero di colori per adattarsi ai limiti della memoria o per ottenere la compressione. 

##Dati

In questo esperimento di esempio si presuppone che un'immagine RGB a 24 bit includa 256 x 256 x 256 colori possibili. È anche possibile creare istogrammi con colori standard basati sui valori di intensità. Un altro approccio consiste nel quantizzare esplicitamente l'immagine e *ridurre* il numero di colori, ad esempio a 16 o 64. In questo modo verrà creato uno spazio più ridotto con, idealmente, meno elementi non significativi e varianza. I dati relativi ai pixel (ogni pixel come riga di set di dati) sono stati quindi passati al modulo di clustering K-Means. 

##Modello

Il modello viene creato come illustrato nell'immagine seguente:

![Model][image1]

Il clustering K-Means è stato eseguito con K=10 fino a 500 in 5 rami diversi. Sono stati prima di tutto calcolati i cluster e quindi il clustering è stato aggregato alla media dei colori di pixel tramite uno script R. 
Ogni pixel è stato infine associato al colore del cluster aggregato e inviato alla nuova immagine in formato CSV. Nel frattempo è stata anche calcolata la differenza della radice quadrata dell'errore quadratico medio dei nuovi colori di pixel con l'immagine originale e i colori sono stati mostrati in un grafico R, usando lo script R Execute. 

##Risultati

L'esito è stato testato in diversi cluster (colori), come mostrato nel modello di esperimento seguente. Come illustrato, un livello maggiore di clustering consente di creare immagini di qualità superiore con minore compressione:

||
------------ | ---------
**Originale** | ![Original][image2a]
**K=10**     | ![K=10][image2b]
**K=20**     | ![K=20][image2c]
**K=50**     | ![K=50][image2d]
**K=100**    | ![K=100][image2e]
**K=500**    | ![K=500][image2f]


È stata anche misurata la precisione tramite la differenza della radice quadrata dell'errore quadratico medio per i colori dell'immagine originale, come illustrato dalla seconda porta di output del modulo dello "script R Execute":

![Output of Execute R Script module][image3]

Come illustrato, maggiore è il numero di cluster di colori, maggiore sarà il numero di colori corrispondenti all'immagine originale (qualità migliore). 

##Codice per convertire immagini in CSV e viceversa

Per inserire le immagini in ML Studio, è stato scritto semplice codice di conversione, che può convertire file di immagine in formato CSV utilizzabile in ML Studio e codice per la conversione del formato CSV in un'immagine. Il codice seguente è disponibile per l'uso. L'aggiunta di un modulo per la lettura nelle immagini è prevista per il futuro. 

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	using System.Drawing;
	using System.Drawing.Imaging;
	using System.IO;
	 
	namespace Text2Image
	{
	    class Program
	    {
	        static void img2csv(string img_path)
	        {
	            FileInfo img_info = new FileInfo(img_path);
	            string destination_file_directory = img_info.DirectoryName + "\\";
	            string destination_file_name = img_info.Name.Remove(img_info.Name.LastIndexOf('.'), 4);
	            string destination_file_path = destination_file_directory + destination_file_name + ".csv";
	 
	            // Read the image
	            Bitmap img = new Bitmap(img_path);
	 
	            // Create the CSV File and write the header values
	            System.IO.StreamWriter file = new System.IO.StreamWriter(destination_file_path);
	            file.WriteLine("X,Y,R,G,B");
	 
	            // Write the Pixel values
	            for (int x = 0; x < img.Width; x++)
	                for (int y = 0; y < img.Height; y++)
	                {
	                    string line = x + "," + y + "," + img.GetPixel(x, y).R + "," + img.GetPixel(x, y).G + "," + img.GetPixel(x, y).B ;
	                    file.WriteLine(line);
	                }
	 
	            file.Close();
	        }
	 
	        static void csv2img(string csv_path)
	        {
	            FileInfo csv_info = new FileInfo(csv_path);
	            string destination_file_directory = csv_info.DirectoryName + "\\";
	            string destination_file_name = csv_info.Name.Remove(csv_info.Name.LastIndexOf('.'), 4);
	            string destination_file_path = destination_file_directory + destination_file_name + ".png";
	            
	            // Read all the lines in the CSV file
	            string[] lines = System.IO.File.ReadAllLines(csv_path);
	 
	            // set a new bitmap image with the provided width and height in the header
	            string[] wh = lines.Last().Split(new Char[] { ' ', ',', '.', ':', '\t', '{', '}' });
	            int img_width = Convert.ToInt32(wh[0])+1;
	            int img_height = Convert.ToInt32(wh[1])+1;
	 
	            Bitmap bmp_img = new Bitmap(img_width, img_height);
	 
	            for (int i = 1; i < lines.Length ;i++ )
	            {
	                string[] values = lines[i].Split(new Char[] { ' ', ',', '.', ':', '\t', '{', '}' });
	                if (values.Length < 3)
	                    continue;
	 
	                int x = Convert.ToInt16(values[0]);
	                int y = Convert.ToInt32(values[1]);
	                int r = Convert.ToInt32(values[2]);
	                int g = Convert.ToInt32(values[3]);
	                int b = Convert.ToInt32(values[4]);
	 
	                bmp_img.SetPixel(x, y, Color.FromArgb(r, g, b));
	            }
	 
	            bmp_img.Save(destination_file_path);
	        }
	 
	        static void Main(string[] args)
	        {
	            string source_path = args[1];
	            FileInfo source_info = new FileInfo(source_path);
	 
	            if (source_info.Extension == ".csv")
	                csv2img(source_path);
	            else
	                img2csv(source_path);
	        }
	    }
	}


## Esperimento di esempio

L'esperimento di esempio seguente associato a questo modello è disponibile in ML Studio nella sezione **ESPERIMENTI** della scheda **ESEMPI**.

> **Esperimento di esempio - Compressione immagini basata sui colori tramite clustering K-Means - Sviluppo**


## Set di dati di esempio

L'esempio di set di dati seguente usato per questo esperimento è disponibile in ML Studio nella tavolozza dei moduli in **Set di dati salvati**.

###Immagine RGB di Bill Gates
[AZURE.INCLUDE [machine-learning-sample-dataset-bill-gates-rgb-image](../includes/machine-learning-sample-dataset-bill-gates-rgb-image.md)]



[image1]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image1.png
[image2a]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2a.jpg
[image2b]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2b.png
[image2c]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2c.png
[image2d]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2d.png
[image2e]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2e.png
[image2f]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2f.png
[image3]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image3.png

