## Apunte: Interfaz de mi calculadora en Flet 
En este apunte se explica como se desarrrolla la interfaz de una calculadora desarrollada en Pyyhon en la libreria Flet oara crear una interfaz grafica. La aplicacion permite realizar operaciones aritmeticas fundamentales como suma, resta, multiplicacion, y division, ademas de funciones adicionales como limpiar la pnatalla (AC), cambiar el signo del numero (+-), calcular porcentajes(%) y mostrar resultados en tiempo real. La calculadora funciona mediante botones interactivos que usan eventos on_click, los cuales ejecutan una funcion central encargada de manejar la logica de las operaciones y actualizar el valor mostrado en la pantalla.
## Importancion de librerias
Permite usar field para configurar atrbibutos dentro de clases que solo guardan datos.
```phyton
from dataclasses import field
import flet as ft
```
## Funcion principal
Definimos la funcion llamada main y se indica que page es un objeto de Flet que representa la ventana principal.
```python
def main(page: ft.Page):
    page.title = "Calc App"
    page.bgcolor = ft.Colors.BLACK
```
## Se crea un objeto de texto que se mostrara en la ventana 

```python
    result = ft.Text(value="0" , color=ft.Colors.WHITE, size=28)
```
## Variables de estado 
Guarda el numero que se esta mostrando en la calculdora y se inicilaiza en 0.
Cada vez que que el usuario presiona un boton numerico, un punto decimal o realiza una operacion, el valor de current se actualiza.
```python
    current = "0"
```
## Guarda el operador matematico que el usuario elija (+,-,*,/).
```python
    operator = None
```
## Guarda el primer numero que se ingreso antes de presionar un operador.
```python
    operand = None
```
## Indica si el siguiente numero que el usuario escriba debe reemplazar el actual.
```python
    reset_next = False
```
## Define una funcion que se ejecuta cuando se presiona un boton.
 "e" es el evento que envia a flet con informacion del boton presionado.
``` python
    def button_clicked(e: ft.ControlEvent):
```
## Permite que la funcion use y modifique las variables que fueron creadas fuera de ellas.
En esta linea le dice a python que las variables current, operator, operand y reset_next no son locales de la funcion button_clicked si no que pertenecen a la funcion main y que se van a modificar desde dentro de button_clicked. Sin nonlocal , python pensaria que estas varibles dentro de button_clicked, y la calculadora no guardaria el estado correcto entre un clic y otro.
 ```python
        nonlocal current, operator, operand, reset_next
 ```
## Obtiene el contenido del boton presionado 
Se guarda si el boton era "1", "+" etc. Se usa luego para saber que accion hacer.
```python
        data = e.control.content 
 ```
## Boton AC 
if data se usa para saber que boton fue presionado y decidir que accion ejecutar en la calculadora, cuando se presiona Ac la calculadora se reinicia y el numero vuelve a "0" se borra el operador y el numero guardado.
 ```python
        if data == "AC":
            current = "0"
            operator= None 
 ```
## Boton +/-
Primero se verifica si el boton presionado es +/-, es decir, el boton que cambia el signo del numero. Despues con if current ! = "0" se comprueba que el numero actual no sea cero, ya que cambiar el signo de cero no tendre efecto.
 ```python
        elif data == "+/-":
            if current != "0":
 ```
Revisa si el numero que esta en pantalla ya es negativo, es decir, si comiennza con el signo -  .
 ```python
                if current.startswith("-"):
  ```
Si el numero es negativo, se elimina el signo - para vovlerlo positivo.
currente [1:] toma la cadena desde la posicion 1 hasta el final, quitadno el primer caracter.
 ```python
                    current = current[1:]
 ```
Si el numero es positivo se le agrega el signo - al inico para convertirlo en negativo.
 ```python
                else:
                    current = "-" + current
 ```
## Botones de numeros
Verifica si el boton presionado es un numero.
Si el display tiene 0 o se debe reiniciar, el numero nuevo reemplaza el actual ademas de cuando se deben concatener los digitos.
```python
        elif data.isdigit():
            if current == "0" or reset_next:
                current = data
                reset_next = False
            else: 
                current += data
 ```
## Boton de punto decimal 
Permite crear numeros decimales  y solo se agrega el punto si aun ni existe en el numero actual.
Evita errores como 3.14.5
```python
        elif data == ".":
            if "." not in current:
                current +="."
 ```
## Botones de operadores 
Se ejecuta cuando el usuario presiona un operador matematico.
```python
        elif data in ["+",  "-", "*", "/"]:
 ```
Si ya habia una operacion pendiente convierte el numero actual en el segundo operand.
```python
            if operator and not reset_next:
                second = float(current)
 ```
# Realiza la operacion matematica correspondiente 
if operator == "+" verifica si el operador seleccionado es una suma al igual que lo hace con la resta , multiplicacion, y division. Mientras que operand += second suman el segundo numero(second) al valor que ya estaba guardado en operand.
Es equivalente a : operand = operand + second 
operand += second resta el segundo numero al valor almacenado en operand lo mismo para multiplicacion y division.
Por ultimo se evita dividir entre cero.
```python
                if operator == "+": operand += second
                elif operator == "-": operand -= second
                elif operator == "*": operand *= second
                elif operator == "/": operand = operand / second if second != 0 else 0
 ```
## Muestra el resultado en pantalla
```python
                current = str(operand)
 ```
## Si no habia operacion previa guarda el numero actual como primer operando.
```python

            else:
                operand = float(current)
 ```
## Guarda el nuevo operador 
Indica que el siguiente numero debe empezar desde cero.
```python
            operator = data 
            reset_next = True 
 ```
## Boton igual (=)
Se ejecuta cuando se presiona = y si existe un operador guardado y se evita que se intente calcular sin una oepracion valida.
```python
        elif data == "=" and operator:
 ```
Convierte el numero que esta en pantalla en el segundo operando 
```python
            second = float(current)
 ```
## Realiza la operacion matematica segun el operador seleccionado 
Se comprueba si la operacion seleccionada fue una suma.

str: funcion de python que sirve para convertir un vlaor en texto.
 ```python
            if operator == "+":
```
Suma el primer numero (operand) y el segundo numero(second) y guarda el resultado en current como texto para mostrarlo en pantalla.
 ```python
                current = str(operand + second)
 ```
Verifica si la operacion es una resta 
 ```python
            elif operator == "-":
                current = str(operand - second)
 ```
Comprueba si la operacion es una multiplicacion.
 ```python
            elif operator == "*":
                current = str(operand * second)
 ```
Verifica si la operacion es una division y si el segundo numero es cero, se muestra "Error" para evitar una division invalida. 
 ```python
            elif operator == "/":
                current = "Error" if second == 0  else str(operand / second)
 ```
## Limpia el operador y el valor guardado 
Indica que el siguiente numero que se escriba emepezara desde cero.
```python
            operator = None
            operand = None
            reset_next= True
```
## Actualiza el texto del display con el nuevo valor
Refesca la ventana para que el cambio se vea en pantalla.
```python
        result.value = current
        e.page.update()
```
## Controles 
 Boton base 
Se crea una clase base para todos los botones con el fin de compartir propiedades comunes entre todos los botones evita duplicar el codigo y facilitar la organizacion del codigo.
```python
    @ft.control 
    class CalcButton(ft.Button):
        expand: int = 1
```
Botones numericos 
Se representa los botones de los numeros y se define como seran los botones cuando luego se usen.
```python
    @ft.control 
    class DigitButton(CalcButton):
        bgcolor: ft.Colors = ft.Colors.WHITE_24
        color: ft.Colors = ft.Colors.WHITE
 ```
Botones de operacion 
Representa los operadores y se define como seran los botones cuando luego se usen.
```python
    @ft.control
    class ActionButton(CalcButton):
        bgcolor: ft.Colors = ft.Colors.ORANGE
        color: ft.Colors = ft.Colors.WHITE
```
Botones extras 
Representa botones especiales como AC o +/- y se define como seran los botones.
```python
    @ft.control 
    class ExtraActionButton(CalcButton):
        bgcolor: ft.Colors = ft.Colors.BLUE_GREY_100
        color: ft.Colors = ft.Colors.BLACK
```
Los colores lo definimos en bgcolor que es el color del fondo del boton y color el color del texto del boton.

DigiButton: fondo gris, texto blanco (numeros).

ActionButton: fondo naranja (operaciones).

ExtraActionButton: fondo gris azulado (AC,+/-).

## Agrega el contenido a la pagian principal 
Todo lo que este dentro de page.add aparecera en la ventana
```python
    page.add
```
## Se crea un contenedor visual es decir el cuerpo de la calculadora
```python
        ft.Container(
            width=350,
            bgcolor=ft.Colors.BLACK,
            border_radius=ft.BorderRadius.all(20),
            padding=20,
```
## Los elementos se colocan uno debajo del otro 
```python
            content=ft.Column(
                controls=[
```
## Pantalla de resultado 
Crea una fila para el display es decir la pantalla de la calculadora donde se mostraran los numeros que se escriben o los resultados de las operaciones.
```python
                    ft.Row (
                        controls=[result],
                        alignment=ft.MainAxisAlignment.END
```
## Crea una fila horizontal
Todo lo que este dentro se mostrara uno al lado del otro.
```python
                    ft.Row(
```
## Lista de botones que apareceran en esa fila 
```python
                        controls=[
```
## Botones  
Se crean los botones de la calculadora y se asgina un evento de clic para que ejecuten la logica correspondiente cuando el usuario los presiona.

```python
                            ExtraActionButton(content="AC", on_click=button_clicked),
                            ExtraActionButton(content="+/-", on_click=button_clicked),
                            ExtraActionButton(content="%", on_click=button_clicked),
```
Boton de division
```python

                            ActionButton(content="/", on_click=button_clicked),
 
```
Crea una fila horizontal dentro de la calculadora
```python

ft.Row
```
Botones numericos 
Son los botones de numeros 
Se usa digitbutton por :  Fondo gris  Texto blanco
 Al presionarlos, llaman a button_clicked.
```python
                            DigitButton(content="7", on_click=button_clicked),
                            DigitButton(content="8", on_click=button_clicked),
                            DigitButton(content="9", on_click=button_clicked),
 ```
##  Boton de multiplicacon 
Usa ActionButton por eso es naranja 
```python
               
                            ActionButton(content="*", on_click=button_clicked),
``` 
## Botones numericos
Se mostrara la fila con los botones 4, 5 , 6 y resta organizados horizontalmente.
```python
                    ft.Row(
                        controls=[
                            DigitButton(content="4", on_click=button_clicked),
                            DigitButton(content="5", on_click=button_clicked),
                            DigitButton(content="6", on_click=button_clicked),
                            ActionButton(content="-", on_click=button_clicked),
``` 
Boton de operacion  resta 
```python
                            ActionButton(content="-", on_click=button_clicked),
``` 
Se mostrara los botones para los numeros 1,2,3 y el boton de suma.
```python
                        controls=[
                            DigitButton(content="1", on_click=button_clicked),
                            DigitButton(content="2", on_click=button_clicked),
                            DigitButton(content="3", on_click=button_clicked),
                            ActionButton(content="+", on_click=button_clicked),

``` 
Boton del numero 0 
expand=2 Hace que ocupe el doble de espacio que los otros botones.
Imita el diseño de las calculadoras reales.
Al presionarlo, llama a button_clicked.
```python
                    ft.Row(
                        controls=[
                            DigitButton(content="0", expand=2, on_click=button_clicked),
```  
Boton punto 
Permite escribir numeros deciales y usa el estilo de los botones numericos.
```python
                            DigitButton(content=".", on_click=button_clicked),
``` 
Boton igual 
Boton para calcular el resultado.
Usa ActionButton, por eso es naranja.
```python
                            ActionButton(content="=", on_click=button_clicked),
``` 
## Inicia la aplicacion en fet y ejecuta la funcion principal
Se construye la interfaz y la logica del programa
ft.rum arranca la app mientras que el main es la funcion principal donde se crea la ventana, se deifine los botones, se configura el display y se manejan los eventos.
```python
ft.run(main)
```
## CODIGO
```python
from dataclasses import field
import flet as ft

def main(page: ft.Page):
    page.title = "Calc App"
    page.bgcolor = ft.Colors.BLACK

    result = ft.Text(value="0" , color=ft.Colors.WHITE, size=28)

    # Variables de estado 
    current = "0"
    operator = None
    operand = None
    reset_next = False 

    def button_clicked(e: ft.ControlEvent):
        nonlocal current, operator, operand, reset_next
        data = e.control.content

        if data == "AC":
            current = "0"
            operator= None 
            operand = None
        elif data == "+/-":
            if current != "0":
                if current.startswith("-"):
                    current = current[1:]
                else:
                    current = "-" + current
        elif data.isdigit():
            if current == "0" or reset_next:
                current = data
                reset_next = False
            else: 
                current += data
           

        elif data == ".":
            if "." not in current:
                current +="."
        elif data == "%":
            current = str(float(current) /100)
        

        elif data in ["+",  "-", "*", "/"]:
            if operator and not reset_next:
                second = float(current)
                if operator == "+": operand += second
                elif operator == "-": operand -= second
                elif operator == "*": operand *= second
                elif operator == "/": operand = operand / second if second != 0 else 0
                current = str(operand)
            else:
                operand = float(current)
            operator = data 
            reset_next = True 

        elif data == "=" and operator:
            second = float(current)

            if operator == "+":
                current = str(operand + second)
            elif operator == "-":
                current = str(operand - second)
            elif operator == "*":
                current = str(operand * second)
            elif operator == "/":
                current = "Error" if second == 0  else str(operand / second)
            
            operator = None
            operand = None
            reset_next= True 
        
        result.value = current
        e.page.update()

        # Controles
    @ft.control 
    class CalcButton(ft.Button):
        expand: int = 1
       
    
    @ft.control 
    class DigitButton(CalcButton):
        bgcolor: ft.Colors = ft.Colors.WHITE_24
        color: ft.Colors = ft.Colors.WHITE
    
    @ft.control
    class ActionButton(CalcButton):
        bgcolor: ft.Colors = ft.Colors.ORANGE
        color: ft.Colors = ft.Colors.WHITE

    @ft.control 
    class ExtraActionButton(CalcButton):
        bgcolor: ft.Colors = ft.Colors.BLUE_GREY_100
        color: ft.Colors = ft.Colors.BLACK

    page.add(
        ft.Container(
            width=350,
            bgcolor=ft.Colors.BLACK,
            border_radius=ft.BorderRadius.all(20),
            padding=20,
            content=ft.Column(
                controls=[
                    ft.Row (
                        controls=[result],
                        alignment=ft.MainAxisAlignment.END

                    ),
                    ft.Row(
                        controls=[
                            ExtraActionButton(content="AC", on_click=button_clicked),
                            ExtraActionButton(content="+/-", on_click=button_clicked),
                            ExtraActionButton(content="%", on_click=button_clicked),
                            ActionButton(content="/", on_click=button_clicked),
                        ]
                    ),
                    ft.Row(
                        controls=[
                            DigitButton(content="7", on_click=button_clicked),
                            DigitButton(content="8", on_click=button_clicked),
                            DigitButton(content="9", on_click=button_clicked),
                            ActionButton(content="*", on_click=button_clicked),
                        
                        ]
                    ),
                    ft.Row(
                        controls=[
                            DigitButton(content="4", on_click=button_clicked),
                            DigitButton(content="5", on_click=button_clicked),
                            DigitButton(content="6", on_click=button_clicked),
                            ActionButton(content="-", on_click=button_clicked),
                                        
                        ]
                    ),
                    ft.Row(
                        controls=[
                            DigitButton(content="1", on_click=button_clicked),
                            DigitButton(content="2", on_click=button_clicked),
                            DigitButton(content="3", on_click=button_clicked),
                            ActionButton(content="+", on_click=button_clicked),
                        ]
                    ),
                    ft.Row(
                        controls=[
                            DigitButton(content="0", expand=2, on_click=button_clicked),
                            DigitButton(content=".", on_click=button_clicked),
                            ActionButton(content="=", on_click=button_clicked),
                        ]
                    ),
                
                ]
            ),
        )
    )

ft.run(main)

```
## CALCULADORA 
<img width="512" height="553" alt="image" src="https://github.com/user-attachments/assets/e3e344eb-780b-4475-bd2e-c08e917165dc" />


