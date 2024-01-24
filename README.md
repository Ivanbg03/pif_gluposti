# pif_gluposti
Метод на разполовяването
Тази метод ни позволява да намираме корени в определен интервал. Изискването е в краищата на този интервал функцията да има различни знаци. Ако това е изпълнено, намираме средата на интервала. В краищата на един от двата получени интервала, функцията със сигурност ще има различни знаци. Намираме средата на този интервала и цикълът продължава, докато не достигнем избраната от нас точност.

# Метод на разполовяването
​
# Импортираме всички нужни библиотеки 
​
import numpy as np
import sympy as sym
import math
import matplotlib.pyplot as plt
from matplotlib import rcParams
from matplotlib import style
rcParams.update({'figure.autolayout': True})
from sympy import latex
from IPython.display import display, Math
​
x = sym.Symbol("x")
​
def find_root(a,b,f,eps):
    root = 0
    k = 0
    while(abs(f.subs(x,root))>eps):  # Цикълът ще се изпълнява, докато не достигне дадената точност
        sgn_left = f.subs(x,a)/abs(f.subs(x,a)) # Знак на функцията в левия край
        sgn_right = f.subs(x,b)/abs(f.subs(x,a)) # Знак на функцията в десния край
        middle = (a+b)/2
        sgn_middle = f.subs(x,middle)/abs(f.subs(x,middle)) # Знак на функцията в средата
        
        if(sgn_middle == sgn_left):
            a = middle
        else:
            b = middle
        root = middle
        k+=1
        if(k>50):
            return "Няма корен в този интервал" # Връща грешка, ако след определен брой итерации, не намери корен
        
    acc = -round(math.log10(eps))
    root = round(root,acc)
    
    print("---------------------------")
    print("Коренът в този интервал е: " + str(root))
​
f = input("Въведете вашата функция: ")
f = sym.sympify(f)
print(f)
latex_code = latex(f)
print(latex_code)
​
x_plot = np.linspace(-5,5,100)
y_plot = []
​
for el in x_plot:
    y_plot.append(f.subs(x,el))
​
plt.plot(x_plot,y_plot,label=f"${latex_code}$")  # Показва как изглежда нашата функция, за да може да зададем примерни граници
zero = np.zeros(100)
plt.plot(x_plot,zero,linestyle="--",color="black",label=r'$f(x)=0$')
plt.xlabel(r'$x$',fontsize=16)
plt.ylabel(r"$f(x)$",fontsize=16)
plt.legend()
plt.show()
​
a = float(input("Въведете лява граница на интервала: "))
b = float(input("Въведете дясна граница на интервала: "))
eps = float(input("Въведете изисквана точност (Например 0.001): "))
    
find_root(a,b,f,eps)
Въведете вашата функция: exp(x)-3
exp(x) - 3
e^{x} - 3

Въведете лява граница на интервала: 0
Въведете дясна граница на интервала: 3
Въведете изисквана точност (Например 0.001): 0.000001
---------------------------
Коренът в този интервал е: 1.098612
Интерполационен сплайн
Всеки интервал от две точки се свързва с така наречения интерполационен сплайн. Може да се избере от каква степен да е кривата. Ако е линеен, то функцията искаме да е непрекъсната в дадените точки, ако е квадратен искам и първата производна да е непрекъсната.

# Интерполационен сплайн
​
import numpy as np
import sympy as sym
import math
import matplotlib.pyplot as plt
from matplotlib import rcParams
from matplotlib import style
rcParams.update({'figure.autolayout': True})
from sympy import latex
from IPython.display import display, Math
​
# Линеен сплайн
​
def find_interval(x,z):
    
    n = len(x)
    
    k = 0
    while(k<n):
        if(x[k]<=z and z<=x[k+1]):
            break
        k+=1
        
    return k 
​
def linear_splain(x,y,z):
    
    n = len(x)
    h = np.zeros(n)
    a = np.zeros(n)
    b = np.zeros(n)
    d_y = np.zeros(n)
    
    i = 1
    while(i<n):
        h[i] = x[i]-x[i-1]
        d_y[i] = y[i]-y[i-1]
        a[i] = y[i-1]
        b[i] = d_y[i]/h[i]
        i+=1
        
    j = 1
    while(j<n):
        x_def = np.linspace(x[j-1],x[j],50)
        plt.plot(x_def,a[j]+b[j]*(x_def-x[j-1]),color="black",linestyle="--")
        j+=1
    
    plt.scatter(x,y,color="black")
    plt.xlabel(r"$x$",fontsize=16)
    plt.ylabel(r"$y$",fontsize=16)
    
    z_index = find_interval(x,z)    
    y_test = a[z_index+1]+b[z_index+1]*(z-x[z_index])
    plt.scatter(z,y_test,color="blue",s = 40)
    
    plt.plot()
    print("---------------------------")
    print("Резултатът от линейния сплайн е " + str(round(y_test,1)))
    
    
def square_splain(x,y,z):
    
    n = len(x)
    h = np.zeros(n)
    a = np.zeros(n)
    b = np.zeros(n)
    c = np.zeros(n)
    d_y = np.zeros(n)
    
    i = 1
    while(i<n):
        h[i] = x[i]-x[i-1]
        d_y[i] = y[i]-y[i-1]
        a[i] = y[i-1]
        if(i!=1):
            b[i] = -b[i-1]+2*(d_y[i-1]/h[i-1])
        c[i] = d_y[i]/h[i]**2-b[i]/h[i]
        i+=1
        
    j = 1
    while(j<n):
        x_def = np.linspace(x[j-1],x[j],50)
        plt.plot(x_def,a[j]+b[j]*(x_def-x[j-1])+c[j]*(x_def-x[j-1])**2,color="black",linestyle="--")
        j+=1
    
    plt.scatter(x,y,color="black")
    plt.xlabel(r"$x$",fontsize=16)
    plt.ylabel(r"$f(x)$",fontsize=16)
    
    z_index = find_interval(x,z)    
    y_test = a[z_index+1]+b[z_index+1]*(z-x[z_index])+c[z_index+1]*(z-x[z_index])**2
    plt.scatter(z,y_test,color="blue",s = 40)
    
    plt.plot()
    print("---------------------------")
    print("Резултатът oт квадратичния сплайн е " + str(round(y_test,1)))
​
n = int(input("Брой точки, които ще въведете: "))
​
x = np.zeros(n) 
y = np.zeros(n)
​
i = 0
while(i<n):
    x[i] = input("Въведете x:")
    y[i] = input("Въведете f(x):")
    i+=1
​
z = float(input("Въведете точката, в която да бъде пресметната функцията:"))
​
splain = int(input("Въведете 1 за линеен сплайн и въведете 2 за квадратичен сплайн:"))
​
if(splain == 1):
    linear_splain(x,y,z)
else:
    square_splain(x,y,z)
​
Брой точки, които ще въведете: 3
Въведете x:2
Въведете f(x):8
Въведете x:4
Въведете f(x):9.9
Въведете x:9
Въведете f(x):12.4
Въведете точката, в която да бъде пресметната функцията:3
Въведете 1 за линеен сплайн и въведете 2 за квадратичен сплайн:1
---------------------------
Резултатът от линейния сплайн е 9.0

Интерполационен полином на Лагранж
Използва се интерполационен полином на Лагранж, който е познат от анализа.

# Интерполация на Лагранж
​
import sympy as sym
import numpy as np
​
x = sym.Symbol("x")
​
def Lagrange_interpolation(x_list,y_list,f):
    L_x = 0 # Полином
    i = 0
    for y_el in y_list:
        P_x = 1 # Базисен полином
        for x_el in x_list:
            if(x_el!=x_list[i]):
                P_x = P_x*(x-x_el)/(x_list[i]-x_el)
        L_x = L_x + P_x*y_el
        i+=1
    
    x_dummy = np.linspace(min(x_list),max(x_list),50)
    y_dummy = np.zeros(50)
    
    i = 0
    while(i<50):
        y_dummy[i]=L_x.subs(x,x_dummy[i])
        i+=1
        
    plt.plot(x_dummy,y_dummy,color='black',linestyle="--")
    plt.scatter(x_list,y_list,color="black")
    plt.scatter(f,L_x.subs(x,f),color="blue",s=50)
    plt.xlabel(r"$x$",fontsize=16)
    plt.ylabel(r"$f(x)$",fontsize=16)
    plt.plot()
    
    print("---------------------------")
    print("Резултатът oт интерполационния полином на Лагранж е " + str(round(L_x.subs(x,f),2)))
        
n = int(input("Брой точки, които ще въведете: "))
​
x_data = np.zeros(n) 
y = np.zeros(n)
​
i = 0
while(i<n):
    x_data[i] = input("Въведете x:")
    y[i] = input("Въведете f(x):")
    i+=1
​
z = float(input("Въведете точката, в която да бъде пресметната функцията:"))
​
Lagrange_interpolation(x_data,y,z)
Брой точки, които ще въведете: 3
Въведете x:0
Въведете f(x):1
Въведете x:1
Въведете f(x):3
Въведете x:2
Въведете f(x):2
Въведете точката, в която да бъде пресметната функцията:1.5
---------------------------
Резултатът oт интерполационния полином на Лагранж е 2.88
---------------------------
Резултатът oт интерполационния полином на Лагранж е 2.88

Метод на най-малките квадрати
Методът минимизира ∑𝑖(𝑐0+𝑐1𝑥𝑖+𝑐2𝑥2𝑖+…+𝑐𝑛𝑥𝑛𝑖−𝑦𝑖)2
print("---------------------------")
# Метод на най-малките квадрати
​
import numpy as np
​
# Метод на Гаус
​
def gaus(matrix):
    dimensions = matrix.shape
    rows = dimensions[0]
    i = 0
    while i<rows:
        matrix[i,:] = matrix[i, :] / matrix[i,i]
        j = 0 
        while j<rows:
            if(j!=i):
                matrix[j, :] = matrix[j, :] - matrix[i, :]*matrix[j,i]
            j+=1
        i+=1
    return matrix[:,rows]
​
# Най-малки квадрати линеен
​
def mnmk(x,y,m):
    
    i = 0
    j = 0
    matrix = np.zeros((m+1, m+2))
    while(i<=m):
        j = 0
        while(j<=m+1):
            if(j!=m+1):
                matrix[i,j] = np.sum(x**(i+j))
            else:
                matrix[i,j] = np.sum(x**i*y)
            j+=1
        i+=1
​
    sol = gaus(matrix)
    a = sol[0]
    b = sol[1]
    
    x_dummy = np.linspace(min(x),max(x),100)
    y_dummy = a + b*x_dummy
    plt.plot(x_dummy,y_dummy,color="black",linestyle="--")
    plt.xlabel(r"$x$",fontsize=16)
    plt.ylabel(r"$f(x)$",fontsize=16)
    plt.scatter(x,y,color="black")
    plt.show()
    
    print("---------------------------")
    print("Линейната регресия е " + str(round(a,2))+ "+" + str(round(b,2)) + "x")
​
​
x = np.array([1, 2, 3, 4, 5])
y = np.array([2, 3, 5, 7, 8])
​
n = int(input("Брой точки, които ще въведете: "))
​
x = np.zeros(n) 
y = np.zeros(n)
​
i = 0
while(i<n):
    x[i] = input("Въведете x:")
    y[i] = input("Въведете f(x):")
    i+=1
​
mnmk(x,y,1)
Брой точки, които ще въведете: 5
Въведете x:1
Въведете f(x):2
Въведете x:2
Въведете f(x):3
Въведете x:3
Въведете f(x):5
Въведете x:4
Въведете f(x):7
Въведете x:5
Въведете f(x):8

---------------------------
Линейната регресия е 0.2+1.6x
Оценка
Оценява се колко е подходяща една матрица за решаване по метода на Гаус

# Дава оценка колко е сингулярна една матрица
​
import numpy as np
import math
​
def condition_number_euclidean(matrix):
    matrix_inv = np.linalg.inv(matrix)
    i = 0
    j = 0
    sum_matrix = 0
    while(i<matrix.shape[0]):
        while(j<matrix.shape[0]):
            sum_matrix+=matrix[i,j]**2
            j+=1
        i+=1
    
    i = 0
    j = 0
    sum_matrix_inv = 0
    while(i<matrix.shape[0]):
        while(j<matrix.shape[0]):
            sum_matrix_inv+=matrix_inv[i,j]**2
            j+=1
        i+=1
        
    norm_m = math.sqrt(sum_matrix)
    norm_i = math.sqrt(sum_matrix_inv)
    return norm_m*norm_i
​
matrix = np.array([[2, 1],
                   [2, 1.001]])
​
condition_number_euclidean(matrix)
1581.9295970429757
LU Декомпозиция
Матрицата на системата 𝐴
 се представя като матрично умножение на долно и горнотриъгълна матрица.

# LU декомпозиция
​
import numpy as np
import math
​
# Извършва LU декомпозицията
​
def LU_composition(A):
    
    n = A.shape[0]
    L = np.zeros((n,n))
    U = np.zeros((n,n))
    
    k = 0
    while(k<n):
        L[k,k]=1
        k+=1
    
    j = 0
    while(j<n):
        i = 0
        while(i<n):
            if(i<=j):
                k = 0
                sum = 0
                while(k<i):
                    sum+= L[i,k]*U[k,j]
                    k+=1
                U[i,j] = A[i,j] - sum
            else:
                k = 0
                sum = 0
                while(k<j):
                    sum+= L[i,k]*U[k,j]
                    k+=1
                L[i,j] = (A[i,j]-sum)/U[j,j]
            i+=1
        j+=1
        
    return L,U
​
# Решава ЛС, след като декомпозира матрицата на системата
​
def LU_composition_solver(A,b):
    
    n = A.shape[0]
    L = LU_composition(A)[0]
    U = LU_composition(A)[1]
    y = np.zeros(n)
    x = np.zeros(n)
    
    i = 0
    while(i<n):
        j = 0
        sum = 0
        while(j<i):
            sum+= L[i,j]*y[j]
            j+=1
        y[i] = (b[i]-sum)/L[i,i]
        i+=1
        
    i = n-1
    while(i>=0):
        j = i+1
        sum = 0
        while(j<n):
            sum+= U[i,j]*x[j]
            j+=1
        x[i] = (y[i]-sum)/U[i,i]
        i-=1
        
    return x
​
# Въвеждане на коефициентите
​
n = int(input("Въведете размерността на вашата система: "))
​
A = np.zeros((n, n)) # Матрица от коефициентите
b = np.zeros(n)
​
i = 0
while(i<n):
    j = 0
    while(j<n):
        A[i,j] = input("Въведете коефициент a" + str(i+1) + str(j+1) + " : ")
        j+=1
    i+=1
​
k = 0
while(k<n):
    b[k] = input("Въведете коефициент b" + str(k+1) + " : ")
    k+=1
    
k = 0
x = LU_composition_solver(A,b)
​
print("---------------------------")
print("Решението на системата е")
while(k<n):
    print("x" + str(k)+ " = " + str(x[k]))
    k+=1 
Въведете размерността на вашата система: 
---------------------------------------------------------------------------
KeyboardInterrupt                         Traceback (most recent call last)
Input In [138], in <cell line: 76>()
     72     return x
     74 # Въвеждане на коефициентите
---> 76 n = int(input("Въведете размерността на вашата система: "))
     78 A = np.zeros((n, n)) # Матрица от коефициентите
     79 b = np.zeros(n)

File ~\anaconda3\lib\site-packages\ipykernel\kernelbase.py:1075, in Kernel.raw_input(self, prompt)
   1071 if not self._allow_stdin:
   1072     raise StdinNotImplementedError(
   1073         "raw_input was called, but this frontend does not support input requests."
   1074     )
-> 1075 return self._input_request(
   1076     str(prompt),
   1077     self._parent_ident["shell"],
   1078     self.get_parent("shell"),
   1079     password=False,
   1080 )

File ~\anaconda3\lib\site-packages\ipykernel\kernelbase.py:1120, in Kernel._input_request(self, prompt, ident, parent, password)
   1117             break
   1118 except KeyboardInterrupt:
   1119     # re-raise KeyboardInterrupt, to truncate traceback
-> 1120     raise KeyboardInterrupt("Interrupted by user") from None
   1121 except Exception:
   1122     self.log.warning("Invalid Message:", exc_info=True)

KeyboardInterrupt: Interrupted by user

import numpy as np
import sympy as sym
import math
​
def finite_difference(y):
    
    n = len(y)
    f_d = np.zeros((n-1,n-1))
    
    i = 0
    while(i<n):
        j = 0
        if(i == 0):
            while(j<n-1):
                f_d[j,i] = y[j+1]-y[j]
                j+=1
        else:
            while(j<n-i-1):
                f_d[j,i] = f_d[j+1, i-1] - f_d[j,i-1]
                j+=1
        i+=1
                
    return f_d
​
def newton_interpolation(x_data,y_data,z):
    
    f_d = finite_difference(y)
    n = len(x_data)
    a = np.ones(n)
    h = x_data[1]-x_data[0]
    
    j = 1
    while(j<n):
        a[j] = f_d[0,j-1]/(math.factorial(j)*h**j)
        j+=1
    
    x = sym.Symbol("x")
​
    N_x = 0
    
    j = 0
    while(j<n):
        i = 0
        product = 1
        while(i<j):
            product *= x-x_data[i]
            i+=1
        N_x += a[j]*product
        j+=1
    
    return (N_x.expand())
​
x_d = [3.6,3.65,3.70,3.75,3.80]
y = [36.598,38.475,40.447,42.521,44.701]
newton_interpolation(x_d,y,2)
−19.9999999999537𝑥4+303.33333333265𝑥3−1703.74999999622𝑥2+4242.53166665736𝑥−3984.60199999142
