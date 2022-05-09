clear
2 R1 = 4;
3 L =0.5;
4 C1 =0.125;
5 V =10;
6
7 s = tf ( ’s ’) ;
8 Xl = s * L ;
9 Xc1 =0;
10
11 if C1 ~= Inf
12 Xc1 = 1/( s * C1 ) ;
13 end
14
15 G = Xl + Xc1 + R1 ;
16
17 % I
18 [ num , den ] = tfdata ( V / G ) ;
19 syms s
20 G_sym = poly2sym ( cell2mat ( num ) ,s ) / poly2sym ( cell2mat ( den ) ,s ) ;
21 I = ilaplace ( G_sym / s ) ; % U ( s ) = 1/ s for the unit step
22
23 % Vr
24 [ num , den ] = tfdata ( R1 * V / G ) ;
25 syms s
26 G_sym = poly2sym ( cell2mat ( num ) ,s ) / poly2sym ( cell2mat ( den ) ,s ) ;
27 Vr1 = ilaplace ( G_sym / s ) ; % U ( s ) = 1/ s for the unit step
28
29 % Vl
30 [ num , den ] = tfdata ( Xl * V / G ) ;
31 syms s
32 G_sym = poly2sym ( cell2mat ( num ) ,s ) / poly2sym ( cell2mat ( den ) ,s ) ;
33 Vl = ilaplace ( G_sym / s ) ;
34
35 % Vc1
36 [ num , den ] = tfdata ( Xc1 * V / G ) ;
37 syms s
38 G_sym = poly2sym ( cell2mat ( num ) ,s ) / poly2sym ( cell2mat ( den ) ,s ) ;
39 Vc1 = ilaplace ( G_sym / s ) ;
40
41 figure
42 subplot (2 ,2 ,1) ;
43 fplot ( Vr1 ,[0 ,5] , ’r ’ , ’ LineWidth ’ ,1) ; title ( ’ Voltage across R1 ’) ;
44 ylabel ( ’ Voltage ( V ) ’) ; xlabel ( ’ Time ( s ) ’) ; grid on
45
46 subplot (2 ,2 ,2) ;
47 fplot ( Vl ,[0 ,5] , ’b ’ , ’ LineWidth ’ ,1) ; title ( ’ Voltage across L ’) ;
48 ylabel ( ’ Voltage ( V ) ’) ; xlabel ( ’ Time ( s ) ’) ; grid on
49
50 subplot (2 ,2 ,3) ;
51 fplot ( Vc1 ,[0 ,5] , ’g ’ , ’ LineWidth ’ ,1) ; title ( ’ Voltage across C1 ’) ;
52 ylabel ( ’ Voltage ( V ) ’) ; xlabel ( ’ Time ( s ) ’) ; grid on
53
54 subplot (2 ,2 ,4) ;
55 fplot (I ,[0 ,5] , ’m ’ , ’ LineWidth ’ ,1) ; title ( ’ Current in the Circuit ’) ;
56 ylabel ( ’ Voltage ( V ) ’) ; xlabel ( ’ Time ( s ) ’) ; grid on


----------------------------------------------------------------------------------------------------------------------------------------------------------------

Motor Simulink:
![image](https://user-images.githubusercontent.com/92109421/167336477-baa78413-c01e-4027-95e5-838bee3f81f4.png)

clear
2 Va =230;
3 Ra =0.478; La =0.003; J =0.462;
4 Ki =3.56; Kb =3.56; Tm = J / B ;
5 TL =325; Ta = La / Ra ; B =0;
6
7 s = tf ( ’s ’) ;
8 Speed = (9.5493 * ( Va * Ki - ( Ra + La * s ) *325 ) ) /(( Ra + La * s ) * s + Ki * Kb ) ;
9 Torque = 325 + (( s *( Va * Ki - ( Ra + La * s ) *325) ) /(( Ra + La * s ) * s + Ki * Kb ) ) ;
10
11 subplot (2 ,1 ,1) ;
12 step ( Speed ) ; title ( ’ Speed of the Motor ’) ;
13 ylabel ( ’ Speed ( rpm ) ’) ; xlabel ( ’ Time ’) ; grid on ;
14 subplot (2 ,1 ,2) ;
15 step ( Torque ) ; title ( ’ Torque of the Motor ’) ;
16 ylabel ( ’ Torque (N - m ) ’) ; xlabel ( ’ Time ’) ; grid on ;







----------------------------------------------------------------------------------------------------------------------------------------------------------------


Routh-
![image](https://user-images.githubusercontent.com/92109421/167336737-e3d7f2aa-f445-42c5-9978-ebda6ccc0fa4.png)

clear ;
2 close all ; clc ;
3 coeffvector = [1 1 2 8 ] ; % [1 18 77 1386];
4 coefflength = length ( coeffvector ) ;
5 rhta blecolou mn = round ( coefflength /2) ;
6 rhtable = zeros ( coefflength , rhtabl ecoloumn ) ;
7 rhtable (1 ,:) = coeffvector (1 ,1:2: coefflength ) ;
8 if rem ( coefflength ,2) == 0
9 rhtable (2 ,:) = coeffvector (1 ,2:2: coefflength ) ;
10 else
11 rhtable (2 ,1: rhtabl ecoloumn - 1) = coeffvector (1 ,2:2: coefflength ) ;
12 end
13
14 epss = 0.01;
15 for i =3: coefflength
16 if rhtable (i -1 ,:) ==0
17 order = coefflength -1;
18 cnt1 =0;
19 cnt2 =0;
20 for j = 1: rhtablecolumn -1
21 rhtable (i -1 , j ) = ( order - cnt1 ) * rhtable (i -2 , cnt2 ) ;
22 cnt2 = cnt2 + 1;
23 cnt1 = cnt1 + 2;
24 end
25 end
26 for j = 1: rhtablecoloumn -1
27 f i r s t e l e m u p p e r r o w = rhtable (i -1 ,1) ;
28 rhtable (i , j ) = ( rhtable (i -1 ,1) * rhtable (i -2 , j +1) - rhtable (i -2 ,1) * rhtable (i -2 , j +1) /
f i r s t e l e m u p p e r r o w ) ;
29 end
30 if rhtable (i ,1) ==0
31 rhtable (i ,1) = epss ;
32 end
33 end
34
35 unstablepoles = 0;
36 for i =1: coefflength -1
37 if sign ( rhtable (i ,1) ) * sign ( rhtable ( i +1 ,1) ) == -1
38 unstablepoles = unstablepoles + 1;
39 end
40 end
41
42 fprintf ( ’\ n Routh Table :\ n ’)
43 rhtable
44 if unstablepoles ==0
45 fprintf ( ’== > It is Stable ’) ;
46 else
47 fprintf ( ’== > It is Unstable ’) ;
48 end
49 fprintf ( ’\ n number of poles on right side = %2.0 f \ n ’ , unstablepoles ) ;
50 sysroots = roots ( coeffvector ) ;
51 fprintf ( ’\ n Roots of the given polynomial :\ n ’) ;
52 sysroots

----------------------------------------------------------------------------------------------------------------------------------------------------------------
Root Locus:
L =0.5; C1 = 0.125;
2 C2 = 0; R1 =0.5;
3 R2 = Inf ; s = tf ( ’s ’) ;
4 XL = s * L ; XC1 = 1/( s * C1 ) ;
5 XC2 = 1/( s * C2 ) ;
6 Z1 = XC1 ; Z2 = R2 + XC2 ;
7 G1 = Z1 /( R1 + XL + Z1 ) ;
8 figure ;
9 rlocus ( G1 ) ;
10 title ( ’ Root locus , when R2 =0 , C2 =0 ’) ;
11 grid on ;

----------------------------------------------------------------------------------------------------------------------------------------------------------------
AC servo meter:

![image](https://user-images.githubusercontent.com/92109421/167336816-eb9d325a-9fe5-42dc-8ee3-417335592327.png)
