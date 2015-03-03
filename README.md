#include <stdio.h>
#include <stdlib.h>

int readMatrix(int matrix, int m, int n) // Функция чтения матрицы, matrix - указатель на матрицу(массив указателей), m - кол-во строк, n - столбцов
{
 if (!matrix || !m || !n) // Если передан хоть один нулевой указатель, т.е. значения некуда записывать
 {
  puts("Неверные параметры.");
  return -1; // Ошибочное завершение, вообще, ненулевой результат - это ошибочное завершение
 }
 puts("Введите количество строк m: ");
 scanf("%d", m); 
 puts("Введите количество столбцов n: ");
 scanf("%d", n);
 if (m <= 0 || n <= 0) // Если введено кол-во строк/столбов <= 0, то ошибка
 {
  printf("Неверная размерность матрицы %dx%d.", m, n);
  return -1;
 }

 matrix = (int**)malloc(sizeof(int)  m); // Выделяем память под массив указателей на строки матрицы, m указателей на int (m  sizeof(int) байт)
 int i = 0, j = 0;
 for (i = 0; i < *m; i++)
  (matrix)[i] = (int*)malloc(sizeof(int)  *n); // Выделяем динамические массивы, строки матрицы по n элементов

 // В цикле читаем элементы матрицы
 for (i = 0; i < *m; i++)
 for (j = 0; j < *n; j++)
 {
  printf("Введите элемент [%d][%d]: ", i, j);
  scanf("%d", &((*matrix)[i][j]));
 }

 return 0;
}

// Считает кол-во положительныъ элементов в массиве (строке матрицы)
int arrayCountPositiveValues(int *A, int size) // size - размер массива
{
 if (size <= 0 || !A) // Если ошибочные параметры
  return -1;

 int i = 0, res = 0;
 for (i = 0; i < size; i++) // Идём по массиву и считаем, сколько раз встретились положительные элементы
 if (A[i] > 0)
  res++;

 return res;
}

// Аналогично, только считаем кол-во торицательных
int arrayCountNegativeValues(int *A, int size)
{
 if (size <= 0 || !A)
  return -1;

 int i = 0, res = 0;
 for (i = 0; i < size; i++)
 if (A[i] < 0)
  res++;

 return res;
}

// Функция создаёт копию матрицы
int copyMatrix(int matrix, int m, int n)
{
 if (!matrix || m <= 0 || n <= 0) // Если ошибки в параметрах
  return -1;

 int result = (int**)malloc(sizeof(int)  m); // Выделяем память под матрицу (аналогично функции readMatrix)
 int i = 0, j = 0;
 for (i = 0; i < m; i++)
  result[i] = (int*)malloc(sizeof(int) * n);

 for (i = 0; i < m; i++)
 for (j = 0; j < n; j++)
  result[i][j] = matrix[i][j]; // В циклах копируем поэлементно матрицу в новую, только что выделенную память

 return result; // Возвращаем указатель на матрицу
}

// Обработка матрицы, создаёт новую матрицу, меняет строки по заданию
int processMatrix(int matrix, int m, int n)
{
 if (m <= 0 || n <= 0 || !matrix)
 {
  return 0;
 }

 // lastMaxPos - максимальное число положительных элементов в строке
 // Neg - отрицательных
 // С пристакой index - номер строки, которой соответствует максимум
 int lastMaxPos = arrayCountPositiveValues(matrix[0], n), lastMaxNeg = arrayCountNegativeValues(matrix[0], n);  // Сначала считаем, что максимальные значения в 0 строке
 int lastMaxPosIndex = 0, lastMaxNegIndex = 0;
 int i = 1;
 int tmpmp = 0, tmpmn = 0;
 for (i = 1; i < m; i++) // Идём в цикле по остальным строкам
 {
  tmpmp = arrayCountPositiveValues(matrix[i], n); // Считаем кол-во положительных
  tmpmn = arrayCountNegativeValues(matrix[i], n); // И отрицательных
  if (tmpmp > lastMaxPos) // Если положительных больше, чем наш текущий максимум
  {
   lastMaxPos = tmpmp; // То обновляем значение
   lastMaxPosIndex = i; // И номер строки
  }
  // Аналогичено для максимума отрицательных чисел
  if (tmpmn > lastMaxNeg)
  {
   lastMaxNeg = tmpmn;
   lastMaxNegIndex = i;
  }
 }

 int **result = copyMatrix(matrix, m, n); // Создаём новую матрицу, копию старой

 // Меняем указатели на 0 строку и строку, в которой больше всего положительных, через временную переменную
 int *ptmp = result[0];
 result[0] = result[lastMaxPosIndex];
 result[lastMaxPosIndex] = ptmp;
 // И указатели на последнюю и строку с макс. кол-вом отрицательных
 ptmp = result[m - 1];
 result[m - 1] = result[lastMaxNegIndex];
 result[lastMaxNegIndex] = ptmp;

 // Возвращаем результат
 return result;
}

// Вывод матрицы
int printMatrix(int **matrix, int m, int n)
{
 if (!matrix || m <= 0 || n <= 0)
  return -1;
 
 int i = 0, j = 0;
 
 for (i = 0; i < m; i++)
 for (j = 0; j < n; j++)
 {
  printf("A[%d][%d] = %d", i, j, matrix[i][j]); // Выводим текущий элемент
  if (!(i == (m - 1) && j == (n - 1))) // Если это не последний элемент
   printf(", "); // то выводим после него запятую
  else
   putchar('.'); // иначе - точку
 }


 return 0;
}

// Удаление матрицы, очистка памяти
int deleteMatrix(int **A, int m)
{
 if (!A || m <= 0)
  return -1;
 int i = 0;
 for (i = 0; i < m; i++) // В цикле проходим все строки и возвращаем память, выделенную под них
  free(A[i]);
 free(angel); // А теперь и под сам массив строк (указателей)
 return 0;
}

int main()
{
 int **matrix = 0;
 int m = 0, n = 0;
 int **newMatrix = 0;

 if (readMatrix(&matrix, &m, &n) == -1)
  return -1;
 
 newMatrix = processMatrix(matrix, m, n);
 
 puts("\nИсходная матрица:");
 printMatrix(matrix, m, n);
 puts("\nСформированная матрица:");
 printMatrix(newMatrix, m, n);
 
 deleteMatrix(matrix, m);
 matrix = 0;
 deleteMatrix(newMatrix, m);
 newMatrix = 0;

 return 0;
}
