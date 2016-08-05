# I2C-on-XC8-PIC16LF18325
My attempt at making a bit-bang I2C function for writing to a LPS22HB using a PIC16LF18325

void LPS_getPressure(void)
{
 
        uint8_t press_h;
        uint8_t press_l;
        uint8_t press_xl;
        SDA_SetDigitalOutput();
        SCLK_SetHigh(); //start condition start
        SDA_SetHigh();
        __delay_ms(.01);
        SDA_SetLow();
        __delay_ms(.01);
        SCLK_SetLow(); 
        __delay_ms(.01); //end start condition
        
        for(int i=0; i<8; i++)     //call LPS address 1 read
        {                          //address is 0b1011100 + Read(1)
                                   //full byte to send: 0b10111001
            if(i==0 || i==2 || i==3 || i==4 || i==7) 
            {
                SDA_SetHigh();
            }
            else
            {
                SDA_SetLow();
            }
            SCLK_SetHigh();
            __delay_ms(.001);
            SCLK_SetLow();
            __delay_ms(.001);
        }
        
         SDA_SetDigitalInput();    //this section is for the ACK
         SCLK_SetHigh();           //released SDA line, do a clock pulse 
         __delay_ms(.01);
         SCLK_SetLow();
         __delay_ms(.01);
         SDA_SetDigitalOutput();   //set back to output
        
           for(int i=0; i<8; i++)  //pressure_XL  register: 0x28
        {
            if(i==2 || i==4)
            {
                SDA_SetHigh();
            }
            else
            {
                SDA_SetLow();
            }
            SCLK_SetHigh();
            __delay_ms(.001);
            SCLK_SetLow();
            __delay_ms(.001);
        }
         
         SDA_SetDigitalInput();    //this section is for the ACK (seconod)
         SCLK_SetHigh();           //released SDA line, do a clock pulse 
         __delay_ms(.01);
         SCLK_SetLow();
         __delay_ms(.01);
         SDA_SetDigitalOutput();   //set back to output
         
        SDA_SetDigitalOutput();
        SCLK_SetHigh(); // repeated start condition start
        SDA_SetHigh();
        __delay_ms(.01);
        SDA_SetLow();
        __delay_ms(.01);
        SCLK_SetLow(); 
        __delay_ms(.01); //end start condition
       
        SDA_SetDigitalInput();
        
        for(int i=0; i<8; i++)   //auto increments, pressure_xl (bottom))
        {
         
            press_xl <<= 1;
            if(SDA_GetValue())
            {
                press_xl = press_xl | 1;
            }
            SCLK_SetHigh();
            __delay_ms(.001);
            SCLK_SetLow();
            __delay_ms(.001);
        }
        
         SDA_SetDigitalInput();    //this section is for the ACK (third)
         SCLK_SetHigh();           //released SDA line, do a clock pulse 
         __delay_ms(.01);
         SCLK_SetLow();
         __delay_ms(.01);
         SDA_SetDigitalOutput();   //set back to output
        
        for(int i=0; i<8; i++)  //auto increments, pressure_l (mid)
        {
         
            press_l <<= 1;
            if(SDA_GetValue())
            {
                press_l = press_l | 1;
            }
            SCLK_SetHigh();
            __delay_ms(.001);
            SCLK_SetLow();
            __delay_ms(.001);
        }
         
         SDA_SetDigitalInput();    //this section is for the ACK (fourth)
         SCLK_SetHigh();           //released SDA line, do a clock pulse 
         __delay_ms(.01);
         SCLK_SetLow();
         __delay_ms(.01);
         SDA_SetDigitalOutput();   //set back to output
         
        for(int i=0; i<8; i++)  //auto increments, pressure_h (top))
        {
         
            press_h <<= 1;
            if(SDA_GetValue())
            {
                press_h = press_h | 1;
            }
            SCLK_SetHigh();
            __delay_ms(.001);
            SCLK_SetLow();
            __delay_ms(.001);
        }
         
         SDA_SetDigitalInput();    //this section is for the ACK (fifth)
         SCLK_SetHigh();           //released SDA line, do a clock pulse 
         __delay_ms(.01);
         SCLK_SetLow();
         __delay_ms(.01);
         
       
        SDA_SetDigitalOutput();
        SCLK_SetHigh(); //stop condition start
        SDA_SetLow();
        __delay_ms(.001);
        SDA_SetHigh();
        __delay_ms(.001);
        SCLK_SetLow(); 
        SDA_SetLow();
        __delay_ms(.001);  //stop condition end
    
}
