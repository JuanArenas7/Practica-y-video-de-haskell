# Practica-y-video-de-haskell
Practica 2 por Juan Andrés Arenas Vasco e Isabella Quintero Acosta
Main.Java: import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;
import java.util.*;
import java.util.stream.Collectors;
import java.util.Comparator;

public class Main {
    public static void main(String[] args) {
        String csvFile = "student-scores.csv"; // Reemplaza con la ubicación de tu archivo CSV

        List<Student> students = new ArrayList<>();

        try (BufferedReader br = new BufferedReader(new FileReader(csvFile))) {
            students = br.lines()
                    .skip(1) // Saltar la primera línea (encabezados)
                    .map(line -> {
                        String[] parts = line.split(",");
                        int id = Integer.parseInt(parts[0]);
                        String first_name = parts[1];
                        String last_name = parts[2];
                        String gender = parts[3];
                        String career_aspiration = parts[4];
                      int math_score = 0;
                      if (parts[5] != null && parts[5].matches("\\d+")) {
                          math_score = Integer.parseInt(parts[5]);
                      } else {
                          System.out.println("parts[5] no contiene un número válido: " + parts[5]);
                      }
                        return new Student(id, first_name, last_name, gender, career_aspiration, math_score);
                    })
                    .collect(Collectors.toList());

            // Filtrar y contar aspirantes por carrera
            aspirantesPorCarrera(students);

            // Total de mujeres por carrera
            totalMujeresPorCarrera(students);

            // Total de hombres por carrera
            totalHombresPorCarrera(students);

            // Estudiante con el puntaje más alto por carrera
            estudianteMejorPuntajePorCarrera(students);

            // Estudiante con el puntaje más alto de todos
            estudianteConMejorPuntajeGlobal(students);

            // Puntaje promedio por carrera
            puntajePromedioPorCarrera(students);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static class Student {
        private int id;
        private String first_name;
        private String last_name;
        private String gender;
        private String career_aspiration;
        private int math_score;

        public Student(int id, String first_name, String last_name, String gender, String career_aspiration, int math_score) {
            this.id = id;
            this.first_name = first_name;
            this.last_name = last_name;
            this.gender = gender;
            this.career_aspiration = career_aspiration;
            this.math_score = math_score;
        }

        public int getId() {
            return id;
        }

        public String getFirst_name() {
            return first_name;
        }

        public String getLast_name() {
            return last_name;
        }

        public String getGender() {
            return gender;
        }

        public String getCareer_aspiration() {
            return career_aspiration;
        }

        public int getMath_score() {
            return math_score;
        }
    }

    static void aspirantesPorCarrera(List<Student> students) {
        System.out.println("Aspirantes por carrera:");
        Map<String, Long> aspirantesPorCarrera = students.stream()
                .collect(Collectors.groupingBy(Student::getCareer_aspiration, Collectors.counting()));

        aspirantesPorCarrera.forEach((carrera, total) -> System.out.println(carrera + ": " + total));
    }

    static void totalMujeresPorCarrera(List<Student> students) {
        System.out.println("Total de mujeres por carrera:");
        Map<String, Long> mujeresPorCarrera = students.stream()
                .filter(student -> "female".equals(student.getGender()))
                .collect(Collectors.groupingBy(Student::getCareer_aspiration, Collectors.counting()));

        mujeresPorCarrera.forEach((carrera, total) -> System.out.println(carrera + ": " + total));
    }

    static void totalHombresPorCarrera(List<Student> students) {
        System.out.println("Total de hombres por carrera:");
        Map<String, Long> hombresPorCarrera = students.stream()
                .filter(student -> "male".equals(student.getGender()))
                .collect(Collectors.groupingBy(Student::getCareer_aspiration, Collectors.counting()));

        hombresPorCarrera.forEach((carrera, total) -> System.out.println(carrera + ": " + total));
    }

    static void estudianteMejorPuntajePorCarrera(List<Student> students) {
      System.out.println("Estudiante con el puntaje más alto por carrera:");
      Map<String, Optional<Student>> estudianteMejorPuntajePorCarrera = students.stream()
              .collect(Collectors.groupingBy(Student::getCareer_aspiration, 
                      Collectors.maxBy(Comparator.comparingInt(Student::getMath_score))));

        estudianteMejorPuntajePorCarrera.forEach((carrera, estudiante) -> {
            if (estudiante.isPresent()) {
                Student bestStudent = estudiante.get();
                System.out.println(carrera + ": " + bestStudent.getFirst_name() + " " + bestStudent.getLast_name());
            }
        });
    }

   static void estudianteConMejorPuntajeGlobal(List<Student> students) {
       System.out.println("Estudiante con el puntaje más alto de todos:");
       Optional<Student> estudianteMejorPuntaje = students.stream()
               .collect(Collectors.maxBy(Comparator.comparingInt(Student::getMath_score)));

       estudianteMejorPuntaje.ifPresent(bestStudent -> {
           System.out.println(bestStudent.getFirst_name() + " " + bestStudent.getLast_name());
       });
   }

    static void puntajePromedioPorCarrera(List<Student> students) {
        System.out.println("Puntaje promedio por carrera:");
        Map<String, Double> puntajePromedioPorCarrera = students.stream()
                .collect(Collectors.groupingBy(Student::getCareer_aspiration, Collectors.averagingInt(Student::getMath_score)));

        puntajePromedioPorCarrera.forEach((carrera, promedio) -> System.out.println(carrera + ": " + promedio));
    }
}
Student.Java: import java.io.IOException;
import java.io.BufferedReader;
import java.io.FileReader;
import java.util.List;
import java.util.stream.Collectors;

public class Student {
    private int id;
    private String first_name;
    private String last_name;
    private String gender;
    private String career_aspiration;
    private int math_score;

    public Student(int id, String first_name, String last_name, String gender, String career_aspiration, int math_score) {
        this.id = id;
        this.first_name = first_name;
        this.last_name = last_name;
        this.gender = gender;
        this.career_aspiration = career_aspiration;
        this.math_score = math_score;
    }

    public int getId() {
        return id;
    }

    public String getFirst_name() {
        return first_name;
    }

    public String getLast_name() {
        return last_name;
    }

    public String getGender() {
        return gender;
    }

    public String getCareer_aspiration() {
        return career_aspiration;
    }

    public int getMath_score() {
        return math_score;
    }
}
