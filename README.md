# Apunte: Interfaz de mi calculadora en Flet 
En este apunte se explica como se desarrrolla la interfaz de la calculadora

# Permite usar field para configurar atrbibutos dentro de clases que solo guardan datos.
from dataclasses import field
# Importa la libreria Flet para crear interfaces graficas.
import flet as ft
# Definimos la funcion llamada main y se indica que page es un objeto de Flet Y representa la ventana principal.
def main(page: ft.Page):
    page.title = "Calc App"
    page.bgcolor = ft.Colors.BLACK

# Se crea un objeto de texto que se mostrara en la ventana. 

    result = ft.Text(value="0" , color=ft.Colors.WHITE, size=28)

# variables de estado 
# Guarda el numero que se esta mostrando en la calculdora y se inicilaiza en 0
 current = "0"  
# Guarda el operador matematico que el usuario elija (+,-,*,/).
operator = None
# Guarda el primer numero que se ingreso antes de presionar un operador.
 operand = None
# Indica si el siguiente numero que el usuario escriba debe reemplazar el actual.
 reset_next = False 

# Define una funcion que se ejecuta cuando se presiona un boton, e es el evento que envia a flet con informacion del boton presionado.
def button_clicked(e: ft.ControlEvent):
# Permite que la funcion use y modifique las variables que fueron creadas fuera de ella sin nonlocal la funcion no podria cambiar esos valores.
 nonlocal current, operator, operand, reset_next
# Obtiene el contenido del boton presionado es decir guarda si el boton era "1", "+" etc-
# Se usa luego para saber que accion hacer.
  data = e.control.content 

# Boton AC 
Cuando se presiona Ac la calculadora se reinicia y el numero vuelve a "0" se borra el operador y el numero guardado.

        if data == "AC":
            current = "0"
            operator= None 
            operand = None

# Boton +/-
Cambia el signo del numero actual si es positivo, lo vuelve negativo y si es negativo lo vuelve positivo y no se hace nada si el numero es 0.
 >elif data == "+/-":
           >if current != "0":
                >if current.startswith("-"):
                    current = current[1:]
                > else:
                    > current = "-" + current

# Botones de numeros
Verifica si el boton presionado es un numero.
Si el display tiene 0 o se debe reiniciar, el numero nuevo reemplaza el actual.
 elif data == "+/-":
            if current != "0":
                if current.startswith("-"):
                    current = current[1:]
                else:
                    current = "-" + current

# Boton punto decimal 
Permite crear numeros decimales  y solo se agrega el punto si aun ni existe en el numero actual.
Evita errores como 3.14.5

        elif data == ".":
            if "." not in current:
                current +="."

# Botones de operadores 
Se ejecuta cuando el usuario presiona un operador matematico.
        elif data in ["+",  "-", "*", "/"]:

# Si ya habia una operacion pendiente convierte el numero actual en el segundo operand.
if operator and not reset_next:
                second = float(current)

# Realiza la operacion matematica correspondiente evita dividir entre cero
 if operator == "+":
                current = str(operand + second)
            elif operator == "-":
                current = str(operand - second)
            elif operator == "*":
                current = str(operand * second)
            elif operator == "/":
                current = "Error" if second == 0  else str(operand / second)
# Muestra el resultado en pantalla.
                current = str(operand)
# Si no habia operacion previa guarda el numero actual como primer operando.
            else:
                operand = float(current)
# Guarda el nuevo operador es decir indica que el siguiente numero debe empezar desde cero.
            operator = data 
            reset_next = True 

# Boton igual (=)
Se ejecuta cuando se presiona = y existe un operador guardado.
Evita que se intente calcular sin una oepracion valida.
        elif data == "=" and operator:
# Convierte el numero que esta en pantalla en el segundo operando 
            second = float(current)
# Realiza la operacion matematica segun el operador seleccionado y en la division evita diviir entre cero mostrando "Error".
            if operator == "+":
                current = str(operand + second)
            elif operator == "-":
                current = str(operand - second)
            elif operator == "*":
                current = str(operand * second)
            elif operator == "/":
                current = "Error" if second == 0  else str(operand / second)

# Limpia el operador y el valor guardado 
Indica que el siguiente numero que se escriba emepezara desde cero.
            operator = None
            operand = None
            reset_next= True 
        
# Actualiza el texto del display con el nuevo valor es decir refesca la ventana para que el cambio se vea en pantalla.
        result.value = current
        e.page.update()
# Boton base 
Se crea una clase base para todos los botones con el fin de compartir propiedades comnes entre todos los botOnes evita duplicar el codigo y facilitar la organizacion del codigo.
    @ft.control 
    class DigitButton(CalcButton):
        bgcolor: ft.Colors = ft.Colors.WHITE_24
        color: ft.Colors = ft.Colors.WHITE

# Botones de operacion 
> Representa los operadores 
>Se define como seran los botones cuando luego se usen.

    @ft.control
    class ActionButton(CalcButton):
        bgcolor: ft.Colors = ft.Colors.ORANGE
        color: ft.Colors = ft.Colors.WHITE
# Botones extras 
Representa botones especiales como AC o +/-
>Se define como seran los botones.

    @ft.control 
    class ExtraActionButton(CalcButton):
        bgcolor: ft.Colors = ft.Colors.BLUE_GREY_100
        color: ft.Colors = ft.Colors.BLACK

# Los colores lo definimos en bgcolor que es el color del fondo del boton y color el color del texto del boton.
DigiButton fondo gris, texto blanco (numeros).
ActionButton fondo naranja (operaciones).
ExtraActionButton fondo gris azulado (AC,+/-).

# Agrega el contenido a la pagian principal todo lo que este dentro de page.add aparecera en la ventana
    page.add
# Se crea un contenedor visual es decir el cuerpo de la calculadora
        ft.Container
          # Ancho fijo del cuerpo de la calculadora.
            width=350,
            bgcolor=ft.Colors.BLACK,  #Fondo negro
            border_radius=ft.BorderRadius.all(20), # Bordes rodondeados.
            padding=20, # Espacio interno entre el borde y el contenido
# Los elementos se colocan uno debajo del otro 
> content=ft.Column
                controls=

# Pantalla de resultado 
crea una fila para el display 
                    ft.Row 
                        controls=[result],
                        alignment=ft.MainAxisAlignment.END

      #Muestra el texto del reusltado.
     # Alinea el texto a la derecha, como una calculadora real.

# Crea una fila horizontal, todo lo que este dentro se mostrara uno al lado del otro.
                    ft.Row
# Lista de botones que apareceran en esa fila 
                        controls=
# Botones de accion extra 
                            ExtraActionButton(content="AC", on_click=button_clicked),
                            ExtraActionButton(content="+/-", on_click=button_clicked),
                            ExtraActionButton(content="%", on_click=button_clicked),

 on_click=button_clicked  Todos llaman a la misma funcion cuando se presionan.

# Boton de division 
                            ActionButton(content="/", on_click=button_clicked),

# Crea una fila horizontal dentro de la calculadora
ft.Row
# Botones numericos 
Son los botones de numeros 
Se usa digitbutton por :
 Fondo gris
 Texto blanco
 Al presionarlos, llaman a button_clicked
   >  DigitButton(content="7", on_click=button_clicked),
                            DigitButton(content="8", on_click=button_clicked),
                            DigitButton(content="9", on_click=button_clicked),
# Boton de multiplicacon 
Usa ActionButton por eso es naranja 
 >ActionButton(content="*", on_click=button_clicked),

# Botones numericos donde se mostrara la fila con los botones 4, 5 , 6 y resta organizados horizontalmente.
Botones para los numeros 4, 5 y 6 
 > ft.Row
    controls=
       DigitButton(content="4", on_click=button_clicked),
        DigitButton(content="5", on_click=button_clicked),
        DigitButton(content="6", on_click=button_clicked),
        

# Boton de operacion  resta 
> ActionButton(content="-", on_click=button_clicked),
# Se mostrara los botones para los numeros 1,2,3 y el boton de suma 
> ft.Row
                        controls=
                            DigitButton(content="1", on_click=button_clicked),
                            DigitButton(content="2", on_click=button_clicked),
                            DigitButton(content="3", on_click=button_clicked),
                            ActionButton(content="+", on_click=button_clicked),


# Crea una fila horizontal en la parte inferior de la calculadora 
> ft.Row

# Boton del numero 0 
                      DigitButton(content="0", expand=2, on_click=button_clicked), 

> expand=2 Hace que ocupe el doble de espacio que los otros botones.
Imita el diseño de las calculadoras reales.
Al presionarlo, llama a button_clicked

#   Boton punto 
Permite escribir numeros deciales y usa el estilo de los botones numericos.
                            DigitButton(content=".", on_click=button_clicked),

# Boton igual 
Boton para calcular el resultado 
Usa ActionButton, por eso es naranja
                            ActionButton(content="=", on_click=button_clicked),

# Inicia la aplicacion en fet y ejecuta la funcion principal donde se construye la interfaz y la logica del progrma

ft.run(main)


