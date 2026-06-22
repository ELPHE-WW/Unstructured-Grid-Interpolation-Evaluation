# NEMO



Currently, the [NEMO v5](https://forge.nemo-ocean.eu/nemo/nemo/-/tree/5.0?ref_type=tags) tag is used to conduct the DBGYRE experiments.



**WORK-IN-PROGRESS** (2026-06-22)



## Details

- Vertical mixing

  - NEMO: `ln_zdfric   = .true.` (this is Pacanowski & Philander 1981)
    - $\nu_0 = 0.01 =$ `rn_avmri = 100.e-4`  ==used to be `0.005` in NEMO==
    - $\nu_b = 0.0001 =$ `rn_avm0 = 0.0001` ==used to be `1.2e-5` in NEMO==
    - $\kappa_b = 1.0 \times 10^{-5} =$ `rn_avt0 = 1.0e-5`
    - $\alpha = 5.0 =$ `rn_alp = 5.`
    - $n = 2 =$ `nn_ric = 2`

- linear EOS

  - NEMO: EOS80 =>? SEOS (`a_0=1.6550e-1`, `b_0=0.`, `lambda*=0.`, `mu*=0.`,`nu=0.`)

    - *TRA/eosbn2.F90*:

      ``` fortran
               DO_3D( kbnd, kbnd, kbnd, kbnd, 1, jpkm1 )
                  zt  = pts  (ji,jj,jk,jp_tem,Knn) - rn_T0
                  zs  = pts  (ji,jj,jk,jp_sal,Knn) - rn_S0
                  zh  = gdept(ji,jj,jk,Knn)
                  ztm = tmask(ji,jj,jk)
                  !
                  zn =  - rn_a0 * ( 1._wp + 0.5_wp*rn_lambda1*zt + rn_mu1*zh ) * zt   &
                     &  + rn_b0 * ( 1._wp - 0.5_wp*rn_lambda2*zs - rn_mu2*zh ) * zs   &
                     &  - rn_nu * zt * zs
                     !
                  prd(ji,jj,jk) = zn * r1_rho0 * ztm                ! density anomaly (masked)
               END_3D
      ```

    - `rho0        = 1026._wp` in *TRA/eosbn2.F90*

    - Rewrite to (==change all occurrences and similar formulations==):   

      ``` fortran
                  !MMS:{
                  ! Linearised EOS like in FESOM.toy.dbgyre
                  ! FESOM: rho = rho0 - rho0 * 0.0002052_wp * (t - 10.0_WP) + rho0 * 0.00079_wp * (s - 35.0_WP)
                  !            = rho0 + rho0 * (- 0.0002052_wp * (t - 10.0_WP) + 0.00079_wp * (s - 35.0_WP) )
                  ! Reduce SEOS with nu=mu=lambda=0. to zn = (-rn_a0*) to:
                  !   zn =                     (-   rn_a0      *      zt       +    rn_b0   *       zs      )
                  !   prhop(ji,jj,jk) = ( rho0 - ( rho0 * zn ) ) ) * ztm
                  ! with rn_a0 = 0.0002052_wp and rn_b0 = 0.00079_wp
                  ! NEMO orig: 
                  !   prhop(ji,jj,jk) = ( rho0 + zn ) * ztm
                  prhop(ji,jj,jk) = ( rho0 + ( rho0 * zn ) ) * ztm
                  !:MMS}
      ```

- boundary condition

  - NEMO: free-slip :white_check_mark:

- actual resolution

  - NEMO: 19.26km

  - ```
    ...
    ```

    

- beta plane

  - NEMO: Beta Plane : `usrdef_hgr.F90:` *beta-plane with regular grid-spacing and rotated domain ==!  (GYRE configuration)*

    - `omega = 7.292116e-05` 

    - `zphi0 = 30._wp` :warning: double def in namelist and code?

    - `ra = 6371229._wp`

    - ``` fortran
          zbeta = 2. * omega * COS( rad * zphi1 ) / ra       ! beta at latitude zphi1
          !SF we overwrite zphi0 (south point in latitude) used just above to define pphif (value of zphi0=15.5190567531966)
          !SF for computation of Coriolis we keep the parameter of Hazeleger, W., and S. S. Drijfhout, JPO 1998.
          zphi0 = 15._wp                                     !  latitude of the most southern grid point
          zf0   = 2. * omega * SIN( rad * zphi0 )            !  compute f0 1st point south
          !
          pff_f(:,:) = ( zf0 + zbeta * ABS( pphif(:,:) - zphi0 ) * rad * ra ) ! f = f0 +beta* y ( y=0 at south)
          pff_t(:,:) = ( zf0 + zbeta * ABS( pphit(:,:) - zphi0 ) * rad * ra ) ! f = f0 +beta* y ( y=0 at south)
      ```

- Surface Fluxes

  - Heat

    - General:

      - $z^* = 28.3$
      - $z_{trp}  = 4.e0$ 
      - already in RAD (thus the $107.0$):

      $$
      T^* = z^* \times cos\left(\frac{\pi*(lat - 5.0)}{107.0}\right)
      $$

      - Downward non-solar heat flux:  

      $$
      Q_{ns} = z_{trp} \left( T_s - T^* \right)
      $$

      

    - NEMO

      - heat flux is **downward positive**

      - ``` fortran
        zTstar    = 28.3      ! intemsity from 28.3 a -5 deg
        ztrp= - 4.e0        ! retroaction term on heat fluxes (W/m2/K)
        
        t_star = zTstar * cos( rpi * ( ( gphit(ji,jj) - 5.0 ) / 107.0 ) )
        
        !MMS: No solar radiation in DBGYRE so far
        !qsr (ji,jj) =  230 * COS( pi * ( gphit(ji,jj) / 162.0 ) )
        qsr(:,:) = 0.0_wp
        
        qns (ji,jj) = ztrp * ( ts(ji,jj,1,jp_tem,Kbb) - t_star ) !- qsr(ji,jj)
        
        ```

      - 
