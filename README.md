# Act-2.3

#Actividad 1.1
Resultados de la primera actividad la cual consistía en determinar el speedup para el programa del cálculo de con 4, 15, 20, 35,50,100,150 threads.
El speedup es un proceso el cual se realiza para poder mejorar el rendimiento de un sistema que procesa un problema determinado, concretamente ayuda a visualizar y mejorar la velocidad de ejecución de una tarea ejecutada en dos arquitecturas similares con recursos diferentes. En esta actividad se puso a prueba el sistema operativo windows 10 con un procesador ryzen 3 y 12 de ram donde se puso a prueba con un código el cual calculaba el número pi variando la cantidad de threads con los que este contaba

Resultados de la computadora con un razen 3/12 de ram

![imagen](https://user-images.githubusercontent.com/57539193/203847386-5dcebf42-1aa3-4edf-9052-ce1cf1798126.png)

Resultados de SSH en el navgador

![imagen](https://user-images.githubusercontent.com/57539193/203848748-3fbddf84-5741-4b49-a7fb-43490231a60d.png)

```cpp
#include <stdio.h>
#include <omp.h>
static long num_pasos=1000000000;
double paso;
#define NUM_THREADS 1
void main(){
    int i, nthreads;
    double pi, sum[NUM_THREADS], t1, t2, tiempo;
    paso=1.0/num_pasos;
    omp_set_num_threads(NUM_THREADS);
    t1 = omp_get_wtime();
    #pragma omp parallel
    {
    int i, id, nthrds;
    double x;
    id = omp_get_thread_num();
    nthrds = omp_get_num_threads();
    if (id==0) nthreads=nthrds;
    for (i=id, sum[id]=0.0;i<num_pasos;i=i+nthrds){
    x=(i+0.5)*paso;
    sum[id]+=4.0/(1.0+x*x);
    }
    }
    for (i=0,pi=0.0;i<nthreads;i++){
    pi+=sum[i]*paso;
    }
    t2=omp_get_wtime();
    tiempo=t2-t1;
    printf("pi = (%lf)\n",pi);
    printf("tomo (%lf) segundos\n", tiempo);
}
```

#Actividad 1.2
Usando paralelización a nivel de tareas en C, determine el número óptimo de puntos a calcular para la solución numérica de una ecuación diferencial de primer orden en un rango determinado. Ocupe al menos dos diferentes ecuaciones para demostrar la eficiencia de su código.

Resultados de la computadora con un razen 3/12 de ram

![imagen](https://user-images.githubusercontent.com/57539193/203856276-faa2336c-b075-4d02-9316-659aa85f71f3.png)

Resultados de SSH en el navgador

![imagen](https://user-images.githubusercontent.com/57539193/203856248-e9750504-926a-4815-80da-da97061916ca.png)

```cpp
#include <stdio.h>
#include <omp.h>
#include <math.h>
#define NUM_THREADS 6

   FILE *fptr;
   FILE *fptr1;
   FILE *fptr2;
   FILE *fptr3;
   FILE *fptr4;
   FILE *fptr5;
   void iteracion(int N,FILE *fptr);
   int N;


int main()
{  omp_set_num_threads(NUM_THREADS);
  //double t[N][NUM_THREADS],w[N][NUM_THREADS];
  double t1;
  double t2;
  double tiempo;

   fptr=fopen("Euler_n_0.txt","w");
   fptr1=fopen("Euler_n_1.txt","w");
   fptr2=fopen("Euler_n_2.txt","w");
   fptr3=fopen("Euler_n_3.txt","w");
   fptr4=fopen("Euler_n_4.txt","w");
   fptr5=fopen("Euler_n_5.txt","w");
t1 = omp_get_wtime();
   #pragma omp parallel
{
     #pragma omp sections
    {
       #pragma omp section
         iteracion(20,fptr);//20//2000900
       #pragma omp section
         iteracion(21,fptr1);//200//2000400
       #pragma omp section
         iteracion(22,fptr2);//2000//2000700
       #pragma omp section
         iteracion(23,fptr3);//20000//2000800
       #pragma omp section
         iteracion(24, fptr4);//200000//2000500
       #pragma omp section
         iteracion(24, fptr5);//2000000//2000100
   }
}

t2=omp_get_wtime();
tiempo=t2-t1;
printf("tomo (%lf) segundos\n", tiempo);
   fclose(fptr);
   fclose(fptr1);
   fclose(fptr2);
   fclose(fptr3);
   fclose(fptr4);
   fclose(fptr5);
   return (0);}
void iteracion(int N, FILE *fptr)
{
      double h;
      double t[N][NUM_THREADS],w[N][NUM_THREADS];
      int i;
      double w0=0.5,a=0,b=2;
      h=(b-a)/N;
      w[0][omp_get_thread_num()] = w0;
      t[0][omp_get_thread_num()] = a;
      for(i = 1;i<N;i++){
          t[i][omp_get_thread_num()]=a+(h*i);
          w[i][omp_get_thread_num()]=w[i-1][omp_get_thread_num()]+h*(5*t[i-1][omp_get_thread_num()]-w[i-1][omp_get_thread_num()]+6);
              fprintf(fptr, "%f\t %f\n", t[i][omp_get_thread_num()], w[i][omp_get_thread_num()]);
          }
         }

```

#Actividad 2.1
Para esta actividad se tuvo que desarrollar un programa en C usando OpenMP que realice el efecto espejo con 20 diferentes imagenes de más de 2000 pixeles por lado.
La primera imagen corresponde a la imagen invertida por la computadora y la segunda correspode a el servicio en la nube

![imagen](https://user-images.githubusercontent.com/57539193/203866074-f38cae96-2e8f-4116-89c7-c769fb1c9c4d.png)


![f5](https://user-images.githubusercontent.com/57539193/203866305-fb671d3d-fb2a-4946-b6e4-4fb3bdc56fdd.jpg)

![f5_1](https://user-images.githubusercontent.com/57539193/203866350-62b6645d-7aef-4ef0-94a6-d428e6906efc.jpg)

Se incluyen las librerías necesarias para poder realizar la paralelización de las imagenes por medio del blur
```cpp
#include <stdlib.h>
#include <stdio.h>
#include<string.h>
#include <png.h>
#include<omp.h>
```
Se abre el archivo que queremos, para poder leerlo y guardar la información a travez de un apuntador, posteriormente el archivo se guarda con el nombre que nosotros queramos colocarle
```cpp
int maskDimensions;

void read_png_file(char *filename,char *outputFileName) {
  int width,height,dimensions;
    png_byte color_type;
    png_byte bit_depth;
    png_bytep *row_pointers = NULL;
    fflush(stdin);
  FILE *fp = fopen(filename, "rb");

  png_structp png = png_create_read_struct(PNG_LIBPNG_VER_STRING, NULL, NULL, NULL);
  if(!png) abort();

  png_infop info = png_create_info_struct(png);
  if(!info) abort();

  if(setjmp(png_jmpbuf(png))) abort();

  png_init_io(png, fp);

  png_read_info(png, info);
 
  width     = png_get_image_width(png, info);
  height    = png_get_image_height(png, info);

  color_type = png_get_color_type(png, info);
  bit_depth  = png_get_bit_depth(png, info);
```
Se toman los valores RGB para tomar la información en cada muestra y corresponde directamente a la resolución de cada muestra.
```cpp
  if(bit_depth == 16)
    png_set_strip_16(png);

  if(color_type == PNG_COLOR_TYPE_PALETTE)
    png_set_palette_to_rgb(png);

```
El color gris toma el valor de 8 o 16 bits de profundidad
```cpp
  if(color_type == PNG_COLOR_TYPE_GRAY && bit_depth < 8)
    png_set_expand_gray_1_2_4_to_8(png);

  if(png_get_valid(png, info, PNG_INFO_tRNS))
    png_set_tRNS_to_alpha(png);
```
Estos color_type no tienen un canal alfa, entonces se rellena con 0xff.
```cpp
  // These color_type don't have an alpha channel then fill it with 0xff.
  if(color_type == PNG_COLOR_TYPE_RGB ||
     color_type == PNG_COLOR_TYPE_GRAY ||
     color_type == PNG_COLOR_TYPE_PALETTE)
    png_set_filler(png, 0xFF, PNG_FILLER_AFTER);
  if(color_type == PNG_COLOR_TYPE_GRAY ||
     color_type == PNG_COLOR_TYPE_GRAY_ALPHA)
    png_set_gray_to_rgb(png);

  png_read_update_info(png, info);
```cpp
Si los apuntadores se encuentran vacios, entonces se aborta la ejecución de esta parte del código
```cpp
  row_pointers = (png_bytep*)malloc(sizeof(png_bytep) * height);
  for(int y = 0; y < height; y++) {
    row_pointers[y] = (png_byte*)malloc(png_get_rowbytes(png,info));
  }
    
  png_read_image(png, row_pointers);
 
  fclose(fp);

  png_destroy_read_struct(&png, &info, NULL);
```
Se toma el tamaño de la mascara que se desea utilizar
```cpp  
  int **maskMatrix;
  int maskD = maskDimensions * maskDimensions;
  maskMatrix = (int**)malloc(maskDimensions*sizeof(int*));
  for(int i=0;i<maskDimensions;i++){
    maskMatrix[i] = (int*)malloc(maskDimensions*sizeof(int));
  }
  for(int i=0;i<maskDimensions;i++){
    for(int j=0;j<maskDimensions;j++)
      maskMatrix[i][j]=1;
  }
```
Con los valores recolectados, utilizamos el tamaño del largo y ancho de la imagen para realizar el blur a la imagen
```cpp
  int size =  height * width;
    int averageR,averageG,averageB;
    int *maskedImageR = (int*)malloc(size*sizeof(int));
    int *maskedImageG = (int*)malloc(size*sizeof(int));
    int *maskedImageB = (int*)malloc(size*sizeof(int));
    int val=0;
    for(int i=0 ; i < height; i++)
    {

          for(int j=0 ; j < width ; j++)
          {

                averageR = 0;
                averageG = 0;
                averageB = 0;

              for(int itImageX = i - maskDimensions/2, itMaskX = 0; itImageX <= ( i+maskDimensions/2 ) && itMaskX < maskDimensions ;itImageX++,itMaskX++)
              {
                      if(itImageX>=0 && itImageX<=height){
                        png_bytep row = row_pointers[itImageX];
                        for(int itImageY = j - maskDimensions/2, itMaskY =0; itImageY <= (j+maskDimensions/2 ) && itMaskY < maskDimensions; itImageY++,itMaskY++)
                        {
                            png_bytep px = &(row[itImageY * 4]);
                            if(itImageX >= 0 && itImageX < height && itImageY >= 0 && itImageY < width){
                                      averageR += (px[0] * maskMatrix[itMaskX][itMaskY]);
                                      averageG += (px[1] *maskMatrix[itMaskX][itMaskY]);
                                      averageB += (px[2] *maskMatrix[itMaskX][itMaskY]);
                            }
                                
                        }
                      }
                    
              } 
              //storing Red
                      if(averageR/maskD > 255)
                        averageR = 255*maskD;
                      else if(averageR/maskD < 0)
                            averageR = 0;
                      maskedImageR[i*width + j] = averageR/maskD;
              
              
              //storing green 
                        if(averageG/maskD > 255)
                          averageG = 255*maskD;
                        else if(averageG/maskD < 0)
                              averageG = 0;
	                      maskedImageG[i*width + j] = averageG/maskD;


              //storing blue
                        if(averageB/maskD > 255)
                          averageB = 255*maskD;
                        else if(averageB/maskD < 0)
                              averageB = 0;
                        maskedImageB[i*width + j] = averageB/maskD;

          }
    }
  
  
      int x=0;
      //storing to the image
      for(int i=0;i<height;i++)
      { 
        png_bytep row = row_pointers[i];
        for(int j=0;j<width;j++)
        {
          png_bytep px = &(row[j * 4]);
          px[0] = maskedImageR[x];
          px[1] = maskedImageG[x];
          px[2] = maskedImageB[x];
          x++;
        }
      }
	  
```
Se inicializan los archivos, diendo que en este caso los archivos que utilizamos son png
```cpp
  fp = fopen(outputFileName, "wb");
  if(!fp) abort();

  png = png_create_write_struct(PNG_LIBPNG_VER_STRING, NULL, NULL, NULL);
  if (!png) abort();

  info = png_create_info_struct(png);
  if (!info) abort();

  if (setjmp(png_jmpbuf(png))) abort();

  png_init_io(png, fp);
  png_set_IHDR(
    png,
    info,
    width, height,
    8,
    PNG_COLOR_TYPE_RGBA,
    PNG_INTERLACE_NONE,
    PNG_COMPRESSION_TYPE_DEFAULT,
    PNG_FILTER_TYPE_DEFAULT
  );
 
  png_write_info(png, info);

    png_write_image(png, row_pointers);
    png_write_end(png, NULL);
    fclose(fp);

    png_destroy_write_struct(&png, &info);
}
```

Se introduce la mascara y se espera a la ejecución del programa anteriormente comentado
```cpp
int main() 
{
	printf("\nEnter the size of Mask:\n");
  scanf("%d",&maskDimensions);
  double startTime = omp_get_wtime();
  #pragma omp parallel
  {
    #pragma omp for schedule(static)
      for(int i=1;i<=800;i++)
      {
              char str[25]="cat (";
                char out[25]="out (";
                int a=i;
                int tmp=i;
                int cnt=0;
                while(tmp)
                {
                    tmp=tmp/10;
                    cnt++;
                }
        
                int j=cnt-1;
                char pok[25]=").png";
                char lok[25];
                while(a)
                {
                    int k=a%10;
                    lok[j]=(char)('0'+k);
                    a=a/10;
                    j--;
                }
                lok[cnt]='\0';
                strcat(str,lok);
                strcat(str,pok);
                strcat(out,lok);
                strcat(out,pok);
                char* s=out;
                char* p=str;
    
        read_png_file(p,s);
        //free(maskMatrix);
      }

  }
  
	double endTime = omp_get_wtime();
	printf("Time taken is %lf",endTime - startTime);
  return 0;
}
```


#Actividad 2.2
Para la actividad 2.2 se tuvo que realizar un gif usando dos diferentes imágenes (<700 pixeles por lado y >2000 pixeles por lado) donde se realizó el efecto de desenfoque y rotación de la imagen. Debe de paralelizar la tarea y compara el tiempo de ejecución con respecto a la tarea realizada sin el uso de threads. Como resultado de su proceso, debe generar 2 archivos .gif (usando algún servicio en la red), con los archivos generados. Al menos 20 imágenes dentro de cada .gif. Deberá entregar un reporte con los resultados obtenidos y el código empleado para las pruebas

Resultados de la computadora con un razen 3/12 de ram

![Captura de pantalla 2022-11-24 144211](https://user-images.githubusercontent.com/57539193/203860785-deebafb0-149d-4ca5-b9e6-d94c910ba54c.png)

Resultados de SSH en el navgador

![imagen](https://user-images.githubusercontent.com/57539193/203858850-7bc1770b-7550-4f4d-b739-5300e0bd59aa.png)

OpenMP_A01173663
Programa en C usando OpenMP que realiza el efecto espejo con 20 diferentes imagenes de más de 2000 pixeles por lado.

En este código lo que se hace es invertir imagenes bmp, este tambien cuenta con la capacidad de poder poner a escalas de grises o en su color la imagen invertida, este código funciona en el lenguaje c y las imagenes utilizadas son de un tamaño superior a 2000 pixeles.

Para poder conseguir este efecto necesitamos utilizar las siguientes librerías:

```cpp
#include <stdio.h>
#include <stdlib.h>
```

En la siguiente parte definimos la cantidad de threads y las variables globales que utizaremos dentro del código.

```cpp
#define NUM_THREADS 6
struct my_pixel{
  unsigned char r, g, b;
};
```
En esta parte del código abrimos las imagenes para poder realizar el espejeo, siendo que en este caso se ha optado por utilizar una función void para poder realizar esto. Dentro de esta función guardamos los valores de las medidas de cada imagen, tambien se declara el nombre de salida del archivo y que nombre tendrá

```cpp
   char *imagenes[]={"nombre_de_imagen"};
    char *nuevas[]={"nombre_de_imagen_resultante"};
    int i, tam= sizeof(imagenes)/sizeof(char *);
    for(int i=0; i<tam;++i){
        FILE *image, *outputImage, *lecturas;
        image = fopen(imagenes[i],"rb");          //Imagen original a transformar
        outputImage = fopen(nuevas[i],"wb");    //Imagen transformada
        long ancho;
        long alto;
        unsigned char r, g, b;               //Pixel
```
Con los valores obtenidos podemos conocer y proyectar las medidas de cada imagen, además estas nos servirán para hacer el espejeo de la imagen
```cpp
        unsigned char xx[54];
        for(int i=0; i<54; i++) {
        xx[i] = fgetc(image);
        fputc(xx[i], outputImage); 
        }
        ancho = (long)xx[20]*65536+(long)xx[19]*256+(long)xx[18];
        alto = (long)xx[24]*65536+(long)xx[23]*256+(long)xx[22];
        printf("largo img %li\n",alto);
        printf("ancho img %li\n",ancho);

```
A continuación se utiliza el padding para poder hacer que la imagen corresponda a una bmp, ya que lo que hace la siguiente parte del código es rellenar los espacios en blanco y así evitar un desplazamiento de esta 
```cpp
padding = ancho%4;
        printf("%d\n", j);
        int count = 0;
        //omp_set_num_threads(4);
        #pragma omp parallel for schedule(dynamic 3) 
        for(int i = 0; i < ancho*alto*3; i+=3){

                b = arr_in [i];
                g = arr_in [i + 1];
                r = arr_in [i + 2];
                unsigned char pixel = 0.21*r+0.72*g+0.07*b;
                // arr_in  [i] = pixel;
                // arr_in  [i + 1] = pixel;
                // arr_in  [i + 2] = pixel;
                arr_in  [i] = b;
                arr_in  [i + 1] = g;
                arr_in  [i + 2] = r;
                count += 3;
                if(count == ancho*3){
                    i+=padding;
                    count = 0;
                }
        for(int i = 0; i < alto; i++){
            for(int j = 0; j < (ancho*3); j+= 3){
                arr_out [(i*((ancho*3)+padding))+j-3] = arr_in  [(i*((ancho*3)+padding))+ (ancho*3) - j];
                arr_out [(i*((ancho*3)+padding))+j-4]  = arr_in  [(i*((ancho*3)+padding))+ (ancho*3 )- j - 1];
                arr_out [(i*((ancho*3)+padding))+j-5]  = arr_in  [(i*((ancho*3)+padding))+ (ancho*3) - j - 2];
            }
        }

        for(int i = 0; i < ancho*alto*3; i++){
            fputc(arr_out [i], outputImage);

```

Por ultimo se crea el archivo de salida y el proceso anteriormente realizado es llamado por el main

```cpp
        fclose(image);
        fclose(outputImage);

    }
    printf("Termine\n"); 
}
int main()
{
    imagemirror();
    return 0;
}
'''


