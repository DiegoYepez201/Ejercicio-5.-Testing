# Ejercicio-5.-Testing
Ejercicio 5. Testing

//Main Activity 

@Composable
fun CostGasLayout() {
    var precioLitroEntrada by remember {
        mutableStateOf("")
    }
    var cantLitrosEntrada by remember {
        mutableStateOf("")
    }
    var propinaEntrada by remember {
        mutableStateOf("")
    }
    var darPropina by remember {
        mutableStateOf(false)
    }

    val precioLitro = precioLitroEntrada.toDoubleOrNull() ?: 0.0
    val cantLitros = cantLitrosEntrada.toDoubleOrNull() ?: 0.0
    val propina = propinaEntrada.toDoubleOrNull() ?: 0.0
    val total = calcularMonto(precioLitro,cantLitros, darPropina = darPropina, propina = propina)

    Column(
        modifier = Modifier.fillMaxSize()
            .padding(15.dp)
            .background(Color.LightGray, shape = RoundedCornerShape(15.dp)),
        verticalArrangement = Arrangement.Center
    ) {
        Text(
            text = stringResource(R.string.calcular_monto),
             modifier= Modifier.fillMaxWidth()
                 .height(50.dp),
            fontWeight = FontWeight.Bold,
            fontSize = 25.sp
            ,
            textAlign = TextAlign.Center
            )

       EditNumberField(
           label = R.string.ingresa_gasolina,
           leadingIcon = R.drawable.money_gas ,
           keyboardsOptions = KeyboardOptions.Default.copy(
               keyboardType = KeyboardType.Number,
               imeAction = ImeAction.Next
           ),
           value = precioLitroEntrada,
           onValueChanged = {precioLitroEntrada = it}
       )
       EditNumberField(
           label = R.string.litros,
           leadingIcon = R.drawable.gasolina ,
           keyboardsOptions = KeyboardOptions.Default.copy(
               keyboardType = KeyboardType.Number,
               imeAction = ImeAction.Next
           ) ,
           value = cantLitrosEntrada,
           onValueChanged = {cantLitrosEntrada = it}
       )
       EditNumberField(
           label = R.string.propina,
           leadingIcon = R.drawable.outline_18_up_rating_24,
           keyboardsOptions = KeyboardOptions.Default.copy(
               keyboardType = KeyboardType.Number,
               imeAction = ImeAction.Done
           ) ,
           value = propinaEntrada,
           onValueChanged = {propinaEntrada = it}
       )
       AddTip(darPropina = darPropina
           , onTipCheckedChange = {darPropina = it}

       )

        Text(
            text = stringResource(R.string.monto_total,total),
            fontWeight = FontWeight.Black,
            fontSize = 30.sp
        )

    }

}

@Composable
fun EditNumberField(
    @StringRes label: Int,
    @DrawableRes leadingIcon: Int,
    keyboardsOptions:KeyboardOptions,
    value: String,
    onValueChanged: (String) -> Unit,
    modifier: Modifier = Modifier
){
    TextField(
        label = { Text(text = stringResource(id = label))  },
        value = value,
        singleLine = true,
        leadingIcon = { Icon(painter = painterResource(id = leadingIcon) , contentDescription = null) },
        keyboardOptions = keyboardsOptions,
        modifier = modifier.fillMaxWidth()
        ,
        onValueChange = onValueChanged,

    )

}


@Composable
fun AddTip(
    darPropina: Boolean,
    onTipCheckedChange: (Boolean) -> Unit,
    modifier: Modifier = Modifier
){
    Row(
        modifier = modifier
            .fillMaxWidth()
            .size(70.dp),
        verticalAlignment = Alignment.CenterVertically
    ) {
        Text(
            text = stringResource(id = R.string.agregar_propina),
            modifier = Modifier.padding(20.dp)
        )
        Switch(
            checked = darPropina ,
            onCheckedChange = onTipCheckedChange
        )
    }


}

@VisibleForTesting
internal fun calcularMonto(precio: Double, cantLitros: Double, darPropina: Boolean, propina:Double ): String{
    var monto = precio * cantLitros
    if ( darPropina){
        monto +=  propina
    }
    return NumberFormat.getCurrencyInstance().format(monto)

}
@Preview(showBackground = true)
@Composable
fun CostGasLayoutPreview() {
    CostoGasolinaTheme {
        CostGasLayout()
    }
}


//CalcularMonto2
package com.mexiti.costogasolina

import org.junit.Assert
import org.junit.Test
import java.text.NumberFormat

class CalcularMonto2 {
    @Test
    fun calcularMonto22_35_40l() {  //22_35 es lo que queremos probar
        val precio = 22.35
        val cantLitros = 40.0
        val darPropina = false
        val propina = 0.0

        val montoEsperado =  NumberFormat.getCurrencyInstance().format(894)
        val montoActual =  calcularMonto(precio,cantLitros,darPropina,propina)

        Assert.assertEquals(
            montoEsperado,
            montoActual
        )  //Vamos a comparar el monto esperado con el monto actual
        //EL assertEquals aparecía en rojo, así que se escribe con el paréntesis y alt+enter y seleccionamos la srgunda opción
    }
}



//CalcularMonto2UITest
package com.mexiti.costogasolina

import androidx.compose.ui.test.junit4.createComposeRule
import androidx.compose.ui.test.onNodeWithText
import androidx.compose.ui.test.performTextInput
import com.mexiti.costogasolina.ui.theme.CostoGasolinaTheme
import org.junit.Rule
import org.junit.Test
import java.text.NumberFormat

class CalcularMonto2UITest {
    @get : Rule         //leer lo que teclee el usuario
    val composeTestRule = createComposeRule()
    @Test
    fun CalcularMonto22_35_40l(){
        composeTestRule.setContent {
            CostoGasolinaTheme {
                CostGasLayout()
            }
        }
        composeTestRule.onNodeWithText("Ingresa precio por litro de Gasolina")
            .performTextInput("22.35")
        composeTestRule.onNodeWithText("Litros")
            .performTextInput("40")

        val montoEsperado = NumberFormat.getCurrencyInstance()
            .format(894)
        composeTestRule.onNodeWithText("Monto Total: $montoEsperado")
            .assertExists("No se encontró nigun nodo con el texto señalado")

    }
}
