# evaluacion2
Se creo una base de datos que esta enfocada a una gestion academica, es un instutucion el cual registra al estudiante, al docente, la materia, los cursos que hay y por ultimo las incripciones, se tilizo sql server para hacer esta base de datos para luego migrar a postgress.
Con esto se espera agilizar lo que seria la gestione de una institucion educativa.

SCRIPT COMPLETO


CREATE DATABASE GestionAcademica;
GO

USE GestionAcademica;
GO




CREATE TABLE Estudiantes (
    id_estudiante INT PRIMARY KEY,
    nombre VARCHAR(50) NOT NULL,
    apellido VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE 
);

CREATE TABLE Docentes (
    id_docente INT PRIMARY KEY,
    nombre VARCHAR(50) NOT NULL,
    especialidad VARCHAR(50),
    salario DECIMAL(10,2) CHECK (salario > 0) 
);


CREATE TABLE Materias (
    id_materia INT PRIMARY KEY,
    nombre VARCHAR(50) NOT NULL,
    creditos INT CHECK (creditos > 0) 
);


CREATE TABLE Cursos (
    id_curso INT PRIMARY KEY,
    id_docente INT,
    id_materia INT,
    FOREIGN KEY (id_docente) REFERENCES Docentes(id_docente),
    FOREIGN KEY (id_materia) REFERENCES Materias(id_materia)
);

CREATE TABLE Inscripciones (
    id_inscripcion INT PRIMARY KEY,
    id_estudiante INT,
    id_curso INT,
    fecha DATE DEFAULT GETDATE(), 
    FOREIGN KEY (id_estudiante) REFERENCES Estudiantes(id_estudiante),
    FOREIGN KEY (id_curso) REFERENCES Cursos(id_curso)
);

GO



INSERT INTO Estudiantes VALUES
(1, 'Juan', 'Perez', 'juan@gmail.com'),
(2, 'Maria', 'Lopez', 'maria@gmail.com'),
(3, 'Carlos', 'Gomez', 'carlos@gmail.com');

INSERT INTO Docentes VALUES
(1, 'Luis', 'Matematicas', 2500),
(2, 'Ana', 'Programacion', 3000),
(3, 'Pedro', 'Fisica', 2800);

INSERT INTO Materias VALUES
(1, 'Algebra', 5),
(2, 'Programacion I', 6),
(3, 'Fisica I', 4);

INSERT INTO Cursos VALUES
(1, 1, 1),
(2, 2, 2),
(3, 3, 3);

INSERT INTO Inscripciones VALUES
(1, 1, 1, GETDATE()),
(2, 2, 2, GETDATE()),
(3, 3, 3, GETDATE());

GO


CREATE INDEX idx_nombre_estudiante
ON Estudiantes(nombre);

GO



CREATE VIEW Vista_Estudiantes_Cursos AS
SELECT 
    E.nombre AS Nombre_Estudiante,
    M.nombre AS Materia,
    D.nombre AS Docente
FROM Inscripciones I
INNER JOIN Estudiantes E ON I.id_estudiante = E.id_estudiante
INNER JOIN Cursos C ON I.id_curso = C.id_curso
INNER JOIN Materias M ON C.id_materia = M.id_materia
INNER JOIN Docentes D ON C.id_docente = D.id_docente;

GO


SELECT nombre, apellido
FROM Estudiantes
WHERE id_estudiante IN (
    SELECT I.id_estudiante
    FROM Inscripciones I
    INNER JOIN Cursos C ON I.id_curso = C.id_curso
    INNER JOIN Materias M ON C.id_materia = M.id_materia
    WHERE M.creditos > 4
);

GO
