Este es un contrato inteligente de solidez para administrar la nómina de un empleado basado en tokens ERC20. El propietario del contrato puede agregar nuevos tokens admitidos en el contrato y hacer una lista blanca de diferentes tipos de tokens para cada empleado, de modo que pueda retirar sus pagos en tokens de acuerdo con una distribución establecida por el empleado.

El token predeterminado admitido es EURT, que es un token que representa la moneda EUR. También agregué el Token USD, USDT. Cualquier transacción que intente enviar ether al contrato de Nómina será revertida. El éter no está permitido.

Los empleados pueden retirar su pago solo una vez al mes y, cada seis meses, pueden definir una nueva distribución de tokens, pero para los tokens incluidos en la lista blanca.

Además de eso, el propietario del contrato puede bloquear/permitir pagos en caso de emergencia. Se encuentra disponible información adicional sobre cada empleado, pagos, distribuciones y tokens admitidos.

En aras de la simplicidad, supongamos que EUR es un token ERC20
// También supongamos que podemos confiar al 100% en el oráculo del tipo de cambio
contrato PayrollInterface {
  /* SOLO PROPIETARIO */
  función addEmployee (dirección dirección de cuenta, dirección [] tokens permitidos, uint256 initialYearlyEURSalary);
  función setEmployeeSalary(uint256 employeeId, uint256 yearlyEURSalary);
  función removeEmployee(uint256 employeeId);

  function addFunds() a pagar;
  función scapeHatch();
  // función añadirFondosToken()? // Usar applyAndCall o ERC223 tokenFallback

  la función getEmployeeCount() constante devuelve (uint256);
  función getEmployee (uint256 employeeId) rendimientos constantes (dirección de empleado); // Devuelve toda la información importante también

  la función calculaPayrollBurnrate () constante devuelve (uint256); // Importe mensual de EUR gastado en salarios
  la función calculaPayrollRunway () constante devuelve (uint256); // Días hasta que el contrato puede quedarse sin fondos

  /* SOLO EMPLEADO */
  función determineAllocation(dirección[] tokens, uint256[] distribución); // solo se puede llamar una vez cada 6 meses
  funcion dia de pago(); // solo disponible una vez al mes

  /* SOLO ORACLE */
  función setExchangeRate (token de dirección, uint256 EURExchangeRate); // usa decimales del token


Consideraciones

    En lugar de pasar una lista de tokens con un tamaño arbitrario para calcular la distribución o recibir pagos, ahora acepta solo 1 token a la vez, por lo que evita problemas con el tamaño máximo de bloque y no tenemos que implementar el almacenamiento en caché de bucles para arreglos arbitrarios. longitud;
    La función de destrucción rescinde el contrato y devuelve los fondos al propietario del contrato;
    Implementó tokens ERC20 EURT y USDT muy básicos sin asignación para transferir fondos de token mediante un contrato de token. El EURT es el token predeterminado. Se pueden agregar fácilmente más fichas; Se basa en tokens de Open Zeppelin lib;
    Supongamos que podemos confiar al 100% en los tipos de cambio de Oracle; Se puede mejorar más adelante;
    El contrato se basa en una dirección Oracle predeterminada y una dirección EURT predeterminada proporcionada a través de argumentos de constructor;
    Se agregaron funciones de respaldo para token y ether;
    Las cuentas de prueba están disponibles en el script script/ganache-cli.sh que utiliza saldos personalizados para ejecutar la prueba de trufa test/PayrollContractTest.js. 
