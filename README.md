MyMotion 


{
    scalar t = time_.value();
    scalar A = amplitude_.z();
    scalar theta = A*cos(omega_*t)- A;
    vector eulerAngles =  vector
                        (
                                0,      // Rotation about X
                                0,      // Rotation about Y
                                theta   // Rotation about Z
                        );     
