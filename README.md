using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Text.Json;

namespace HospitalManagementSystem
{
    // =========================================================
    // MODELS
    // =========================================================

    public class Patient
    {
        public int Id { get; set; }
        public string PatientNumber { get; set; } = "";
        public string Name { get; set; } = "";
        public int Age { get; set; }
        public string Gender { get; set; } = "";
        public string Phone { get; set; } = "";
        public string Address { get; set; } = "";
        public string BloodGroup { get; set; } = "";
        public string EmergencyContact { get; set; } = "";
    }

    public class Doctor
    {
        public int Id { get; set; }
        public string DoctorNumber { get; set; } = "";
        public string Name { get; set; } = "";
        public string Specialization { get; set; } = "";
        public string Phone { get; set; } = "";
        public string Email { get; set; } = "";
        public string RoomNumber { get; set; } = "";
        public double ConsultationFee { get; set; }
    }

    public class Appointment
    {
        public int Id { get; set; }
        public int PatientId { get; set; }
        public int DoctorId { get; set; }
        public DateTime Date { get; set; }
        public string Time { get; set; } = "";
        public string Reason { get; set; } = "";
        public string Status { get; set; } = "Scheduled";
    }

    public class Admission
    {
        public int Id { get; set; }
        public int PatientId { get; set; }
        public int DoctorId { get; set; }
        public string RoomNumber { get; set; } = "";
        public string BedNumber { get; set; } = "";
        public DateTime AdmissionDate { get; set; }
        public DateTime? DischargeDate { get; set; }
        public string Diagnosis { get; set; } = "";
        public string Status { get; set; } = "Admitted";
    }

    public class Prescription
    {
        public int Id { get; set; }
        public int PatientId { get; set; }
        public int DoctorId { get; set; }
        public DateTime Date { get; set; }
        public string Medicine { get; set; } = "";
        public string Dosage { get; set; } = "";
        public string Duration { get; set; } = "";
        public string Instructions { get; set; } = "";
    }

    public class Bill
    {
        public int Id { get; set; }
        public string BillNumber { get; set; } = "";
        public int PatientId { get; set; }
        public string BillType { get; set; } = "";
        public double Amount { get; set; }
        public double PaidAmount { get; set; }
        public DateTime Date { get; set; }
        public string Status { get; set; } = "Unpaid";
    }

    public class Payment
    {
        public int Id { get; set; }
        public string ReceiptNumber { get; set; } = "";
        public int PatientId { get; set; }
        public int BillId { get; set; }
        public double Amount { get; set; }
        public DateTime Date { get; set; }
        public string PaymentMethod { get; set; } = "";
    }

    public class HospitalData
    {
        public List<Patient> Patients { get; set; } = new();
        public List<Doctor> Doctors { get; set; } = new();
        public List<Appointment> Appointments { get; set; } = new();
        public List<Admission> Admissions { get; set; } = new();
        public List<Prescription> Prescriptions { get; set; } = new();
        public List<Bill> Bills { get; set; } = new();
        public List<Payment> Payments { get; set; } = new();
    }

    // =========================================================
    // MAIN PROGRAM
    // =========================================================

    class Program
    {
        static HospitalData db = new HospitalData();

        static string dataFile = "hospital_data.json";

        static JsonSerializerOptions jsonOptions =
            new JsonSerializerOptions
            {
                WriteIndented = true
            };

        static void Main()
        {
            LoadData();

            while (true)
            {
                Console.Clear();
                Header();

                Console.WriteLine("1. Patient Management");
                Console.WriteLine("2. Doctor Management");
                Console.WriteLine("3. Appointment Management");
                Console.WriteLine("4. Admission & Discharge");
                Console.WriteLine("5. Prescription Management");
                Console.WriteLine("6. Billing & Payments");
                Console.WriteLine("7. Search");
                Console.WriteLine("8. Hospital Dashboard");
                Console.WriteLine("0. Exit");

                Console.Write("\nEnter option: ");
                string choice = Console.ReadLine() ?? "";

                switch (choice)
                {
                    case "1":
                        PatientMenu();
                        break;

                    case "2":
                        DoctorMenu();
                        break;

                    case "3":
                        AppointmentMenu();
                        break;

                    case "4":
                        AdmissionMenu();
                        break;

                    case "5":
                        PrescriptionMenu();
                        break;

                    case "6":
                        BillingMenu();
                        break;

                    case "7":
                        SearchMenu();
                        break;

                    case "8":
                        Dashboard();
                        break;

                    case "0":
                        SaveData();
                        Console.WriteLine("\nData saved successfully.");
                        Console.WriteLine("Thank you for using Hospital Management System.");
                        return;

                    default:
                        Console.WriteLine("\nInvalid option.");
                        Pause();
                        break;
                }
            }
        }

        // =========================================================
        // HEADER
        // =========================================================

        static void Header()
        {
            Console.ForegroundColor = ConsoleColor.Cyan;

            Console.WriteLine("====================================================");
            Console.WriteLine("          HOSPITAL MANAGEMENT SYSTEM");
            Console.WriteLine("====================================================");

            Console.ResetColor();
            Console.WriteLine();
        }

        // =========================================================
        // PATIENT MANAGEMENT
        // =========================================================

        static void PatientMenu()
        {
            while (true)
            {
                Console.Clear();
                Header();

                Console.WriteLine("PATIENT MANAGEMENT");
                Console.WriteLine("------------------");
                Console.WriteLine("1. Add Patient");
                Console.WriteLine("2. View Patients");
                Console.WriteLine("3. Search Patient");
                Console.WriteLine("4. Update Patient");
                Console.WriteLine("5. Delete Patient");
                Console.WriteLine("6. Patient Details");
                Console.WriteLine("0. Back");

                Console.Write("\nEnter option: ");
                string choice = Console.ReadLine() ?? "";

                switch (choice)
                {
                    case "1":
                        AddPatient();
                        break;

                    case "2":
                        ViewPatients();
                        break;

                    case "3":
                        SearchPatient();
                        break;

                    case "4":
                        UpdatePatient();
                        break;

                    case "5":
                        DeletePatient();
                        break;

                    case "6":
                        PatientDetails();
                        break;

                    case "0":
                        return;

                    default:
                        Console.WriteLine("Invalid option.");
                        Pause();
                        break;
                }
            }
        }

        static void AddPatient()
        {
            Console.Clear();
            Header();

            Console.WriteLine("ADD NEW PATIENT");
            Console.WriteLine();

            Patient patient = new Patient();

            patient.Id = NextPatientId();
            patient.PatientNumber =
                "PAT-" + DateTime.Now.ToString("yyyyMMdd") +
                "-" + patient.Id.ToString("D4");

            Console.Write("Patient Name: ");
            patient.Name = Console.ReadLine() ?? "";

            patient.Age = ReadInt("Age: ");

            Console.Write("Gender: ");
            patient.Gender = Console.ReadLine() ?? "";

            Console.Write("Phone: ");
            patient.Phone = Console.ReadLine() ?? "";

            Console.Write("Address: ");
            patient.Address = Console.ReadLine() ?? "";

            Console.Write("Blood Group: ");
            patient.BloodGroup = Console.ReadLine() ?? "";

            Console.Write("Emergency Contact: ");
            patient.EmergencyContact = Console.ReadLine() ?? "";

            db.Patients.Add(patient);

            SaveData();

            Console.WriteLine();
            Console.WriteLine("Patient added successfully.");
            Console.WriteLine(
                $"Patient Number: {patient.PatientNumber}"
            );

            Pause();
        }

        static void ViewPatients()
        {
            Console.Clear();
            Header();

            Console.WriteLine("PATIENT LIST");
            Console.WriteLine();

            if (!db.Patients.Any())
            {
                Console.WriteLine("No patients found.");
                Pause();
                return;
            }

            Console.WriteLine(
                "{0,-5} {1,-16} {2,-25} {3,-5} {4,-15}",
                "ID",
                "Patient No",
                "Name",
                "Age",
                "Blood Group"
            );

            Console.WriteLine(new string('-', 75));

            foreach (var patient in db.Patients)
            {
                Console.WriteLine(
                    "{0,-5} {1,-16} {2,-25} {3,-5} {4,-15}",
                    patient.Id,
                    patient.PatientNumber,
                    patient.Name,
                    patient.Age,
                    patient.BloodGroup
                );
            }

            Pause();
        }

        static void SearchPatient()
        {
            Console.Clear();
            Header();

            Console.Write("Enter patient name or patient number: ");

            string search = Console.ReadLine() ?? "";

            var patients = db.Patients
                .Where(p =>
                    p.Name.Contains(
                        search,
                        StringComparison.OrdinalIgnoreCase)
                    ||
                    p.PatientNumber.Contains(
                        search,
                        StringComparison.OrdinalIgnoreCase))
                .ToList();

            Console.WriteLine();

            if (!patients.Any())
            {
                Console.WriteLine("No patient found.");
            }
            else
            {
                foreach (var p in patients)
                {
                    Console.WriteLine(
                        $"ID: {p.Id} | " +
                        $"Number: {p.PatientNumber} | " +
                        $"Name: {p.Name} | " +
                        $"Phone: {p.Phone}"
                    );
                }
            }

            Pause();
        }

        static void UpdatePatient()
        {
            ViewPatients();

            int id = ReadInt("Enter Patient ID: ");

            var patient =
                db.Patients.FirstOrDefault(
                    p => p.Id == id
                );

            if (patient == null)
            {
                Console.WriteLine("Patient not found.");
                Pause();
                return;
            }

            Console.Write("New Name: ");
            patient.Name =
                Console.ReadLine() ?? patient.Name;

            patient.Age =
                ReadInt("New Age: ");

            Console.Write("New Gender: ");
            patient.Gender =
                Console.ReadLine() ?? patient.Gender;

            Console.Write("New Phone: ");
            patient.Phone =
                Console.ReadLine() ?? patient.Phone;

            Console.Write("New Address: ");
            patient.Address =
                Console.ReadLine() ?? patient.Address;

            Console.Write("New Blood Group: ");
            patient.BloodGroup =
                Console.ReadLine() ?? patient.BloodGroup;

            Console.Write("New Emergency Contact: ");
            patient.EmergencyContact =
                Console.ReadLine() ?? patient.EmergencyContact;

            SaveData();

            Console.WriteLine("Patient updated successfully.");

            Pause();
        }

        static void DeletePatient()
        {
            ViewPatients();

            int id = ReadInt("Enter Patient ID: ");

            var patient =
                db.Patients.FirstOrDefault(
                    p => p.Id == id
                );

            if (patient == null)
            {
                Console.WriteLine("Patient not found.");
                Pause();
                return;
            }

            Console.Write(
                $"Delete {patient.Name}? (Y/N): "
            );

            string confirm =
                (Console.ReadLine() ?? "").ToUpper();

            if (confirm == "Y")
            {
                db.Patients.Remove(patient);

                db.Appointments.RemoveAll(
                    a => a.PatientId == id
                );

                db.Admissions.RemoveAll(
                    a => a.PatientId == id
                );

                db.Prescriptions.RemoveAll(
                    p => p.PatientId == id
                );

                db.Bills.RemoveAll(
                    b => b.PatientId == id
                );

                db.Payments.RemoveAll(
                    p => p.PatientId == id
                );

                SaveData();

                Console.WriteLine(
                    "Patient deleted successfully."
                );
            }

            Pause();
        }

        static void PatientDetails()
        {
            ViewPatients();

            int id = ReadInt("Enter Patient ID: ");

            var patient =
                db.Patients.FirstOrDefault(
                    p => p.Id == id
                );

            if (patient == null)
            {
                Console.WriteLine("Patient not found.");
                Pause();
                return;
            }

            Console.Clear();
            Header();

            Console.WriteLine("PATIENT DETAILS");
            Console.WriteLine();

            Console.WriteLine(
                $"Patient Number : {patient.PatientNumber}"
            );

            Console.WriteLine(
                $"Name           : {patient.Name}"
            );

            Console.WriteLine(
                $"Age            : {patient.Age}"
            );

            Console.WriteLine(
                $"Gender         : {patient.Gender}"
            );

            Console.WriteLine(
                $"Phone          : {patient.Phone}"
            );

            Console.WriteLine(
                $"Address        : {patient.Address}"
            );

            Console.WriteLine(
                $"Blood Group    : {patient.BloodGroup}"
            );

            Console.WriteLine(
                $"Emergency      : {patient.EmergencyContact}"
            );

            Console.WriteLine();

            var appointments =
                db.Appointments
                .Where(a => a.PatientId == id)
                .OrderByDescending(a => a.Date)
                .ToList();

            Console.WriteLine("APPOINTMENTS");
            Console.WriteLine("------------");

            foreach (var appointment in appointments)
            {
                var doctor =
                    db.Doctors.FirstOrDefault(
                        d => d.Id == appointment.DoctorId
                    );

                Console.WriteLine(
                    $"{appointment.Date:yyyy-MM-dd} " +
                    $"{appointment.Time} | " +
                    $"Dr. {doctor?.Name ?? "Unknown"} | " +
                    $"{appointment.Status}"
                );
            }

            var admissions =
                db.Admissions
                .Where(a => a.PatientId == id)
                .ToList();

            Console.WriteLine("\nADMISSION STATUS");

            foreach (var admission in admissions)
            {
                Console.WriteLine(
                    $"Room {admission.RoomNumber}, " +
                    $"Bed {admission.BedNumber} | " +
                    $"{admission.Status}"
                );
            }

            Pause();
        }

        // =========================================================
        // DOCTOR MANAGEMENT
        // =========================================================

        static void DoctorMenu()
        {
            while (true)
            {
                Console.Clear();
                Header();

                Console.WriteLine("DOCTOR MANAGEMENT");
                Console.WriteLine("-----------------");
                Console.WriteLine("1. Add Doctor");
                Console.WriteLine("2. View Doctors");
                Console.WriteLine("3. Search Doctor");
                Console.WriteLine("4. Update Doctor");
                Console.WriteLine("5. Delete Doctor");
                Console.WriteLine("0. Back");

                Console.Write("\nEnter option: ");

                string choice =
                    Console.ReadLine() ?? "";

                switch (choice)
                {
                    case "1":
                        AddDoctor();
                        break;

                    case "2":
                        ViewDoctors();
                        break;

                    case "3":
                        SearchDoctor();
                        break;

                    case "4":
                        UpdateDoctor();
                        break;

                    case "5":
                        DeleteDoctor();
                        break;

                    case "0":
                        return;

                    default:
                        Console.WriteLine("Invalid option.");
                        Pause();
                        break;
                }
            }
        }

        static void AddDoctor()
        {
            Console.Clear();
            Header();

            Doctor doctor = new Doctor();

            doctor.Id = NextDoctorId();

            doctor.DoctorNumber =
                "DOC-" + doctor.Id.ToString("D4");

            Console.Write("Doctor Name: ");
            doctor.Name = Console.ReadLine() ?? "";

            Console.Write("Specialization: ");
            doctor.Specialization =
                Console.ReadLine() ?? "";

            Console.Write("Phone: ");
            doctor.Phone = Console.ReadLine() ?? "";

            Console.Write("Email: ");
            doctor.Email = Console.ReadLine() ?? "";

            Console.Write("Room Number: ");
            doctor.RoomNumber =
                Console.ReadLine() ?? "";

            doctor.ConsultationFee =
                ReadDouble("Consultation Fee: ");

            db.Doctors.Add(doctor);

            SaveData();

            Console.WriteLine();
            Console.WriteLine("Doctor added successfully.");
            Console.WriteLine(
                $"Doctor Number: {doctor.DoctorNumber}"
            );

            Pause();
        }

        static void ViewDoctors()
        {
            Console.Clear();
            Header();

            Console.WriteLine("DOCTOR LIST");
            Console.WriteLine();

            if (!db.Doctors.Any())
            {
                Console.WriteLine("No doctors found.");
                Pause();
                return;
            }

            foreach (var doctor in db.Doctors)
            {
                Console.WriteLine(
                    $"ID: {doctor.Id} | " +
                    $"No: {doctor.DoctorNumber} | " +
                    $"Dr. {doctor.Name} | " +
                    $"Specialization: {doctor.Specialization} | " +
                    $"Room: {doctor.RoomNumber} | " +
                    $"Fee: {doctor.ConsultationFee:C}"
                );
            }

            Pause();
        }

        static void SearchDoctor()
        {
            Console.Clear();
            Header();

            Console.Write(
                "Enter doctor name or specialization: "
            );

            string search =
                Console.ReadLine() ?? "";

            var doctors =
                db.Doctors
                .Where(d =>
                    d.Name.Contains(
                        search,
                        StringComparison.OrdinalIgnoreCase)
                    ||
                    d.Specialization.Contains(
                        search,
                        StringComparison.OrdinalIgnoreCase))
                .ToList();

            Console.WriteLine();

            if (!doctors.Any())
            {
                Console.WriteLine("No doctor found.");
            }
            else
            {
                foreach (var d in doctors)
                {
                    Console.WriteLine(
                        $"ID: {d.Id} | " +
                        $"Dr. {d.Name} | " +
                        $"{d.Specialization} | " +
                        $"Room {d.RoomNumber}"
                    );
                }
            }

            Pause();
        }

        static void UpdateDoctor()
        {
            ViewDoctors();

            int id = ReadInt("Doctor ID: ");

            var doctor =
                db.Doctors.FirstOrDefault(
                    d => d.Id == id
                );

            if (doctor == null)
            {
                Console.WriteLine("Doctor not found.");
                Pause();
                return;
            }

            Console.Write("Name: ");
            doctor.Name =
                Console.ReadLine() ?? doctor.Name;

            Console.Write("Specialization: ");
            doctor.Specialization =
                Console.ReadLine() ?? doctor.Specialization;

            Console.Write("Phone: ");
            doctor.Phone =
                Console.ReadLine() ?? doctor.Phone;

            Console.Write("Email: ");
            doctor.Email =
                Console.ReadLine() ?? doctor.Email;

            Console.Write("Room Number: ");
            doctor.RoomNumber =
                Console.ReadLine() ?? doctor.RoomNumber;

            doctor.ConsultationFee =
                ReadDouble("Consultation Fee: ");

            SaveData();

            Console.WriteLine("Doctor updated.");

            Pause();
        }

        static void DeleteDoctor()
        {
            ViewDoctors();

            int id = ReadInt("Doctor ID: ");

            var doctor =
                db.Doctors.FirstOrDefault(
                    d => d.Id == id
                );

            if (doctor == null)
            {
                Console.WriteLine("Doctor not found.");
                Pause();
                return;
            }

            db.Doctors.Remove(doctor);

            SaveData();

            Console.WriteLine("Doctor deleted.");

            Pause();
        }

        // =========================================================
        // APPOINTMENT MANAGEMENT
        // =========================================================

        static void AppointmentMenu()
        {
            while (true)
            {
                Console.Clear();
                Header();

                Console.WriteLine("APPOINTMENT MANAGEMENT");
                Console.WriteLine("----------------------");
                Console.WriteLine("1. Book Appointment");
                Console.WriteLine("2. View Appointments");
                Console.WriteLine("3. Cancel Appointment");
                Console.WriteLine("4. Complete Appointment");
                Console.WriteLine("0. Back");

                Console.Write("\nEnter option: ");

                string choice =
                    Console.ReadLine() ?? "";

                switch (choice)
                {
                    case "1":
                        BookAppointment();
                        break;

                    case "2":
                        ViewAppointments();
                        break;

                    case "3":
                        CancelAppointment();
                        break;

                    case "4":
                        CompleteAppointment();
                        break;

                    case "0":
                        return;

                    default:
                        Console.WriteLine("Invalid option.");
                        Pause();
                        break;
                }
            }
        }

        static void BookAppointment()
        {
            ViewPatients();

            int patientId =
                ReadInt("Patient ID: ");

            if (!db.Patients.Any(
                p => p.Id == patientId))
            {
                Console.WriteLine("Patient not found.");
                Pause();
                return;
            }

            ViewDoctors();

            int doctorId =
                ReadInt("Doctor ID: ");

            if (!db.Doctors.Any(
                d => d.Id == doctorId))
            {
                Console.WriteLine("Doctor not found.");
                Pause();
                return;
            }

            Console.Write(
                "Appointment Date (yyyy-MM-dd): "
            );

            DateTime date;

            if (!DateTime.TryParse(
                Console.ReadLine(),
                out date))
            {
                Console.WriteLine("Invalid date.");
                Pause();
                return;
            }

            Console.Write("Appointment Time: ");
            string time =
                Console.ReadLine() ?? "";

            Console.Write("Reason: ");
            string reason =
                Console.ReadLine() ?? "";

            Appointment appointment =
                new Appointment
                {
                    Id = NextAppointmentId(),
                    PatientId = patientId,
                    DoctorId = doctorId,
                    Date = date,
                    Time = time,
                    Reason = reason,
                    Status = "Scheduled"
                };

            db.Appointments.Add(appointment);

            SaveData();

            Console.WriteLine(
                "Appointment booked successfully."
            );

            Pause();
        }

        static void ViewAppointments()
        {
            Console.Clear();
            Header();

            Console.WriteLine("APPOINTMENTS");
            Console.WriteLine();

            if (!db.Appointments.Any())
            {
                Console.WriteLine("No appointments.");
                Pause();
                return;
            }

            foreach (var a in db.Appointments
                .OrderByDescending(x => x.Date))
            {
                var patient =
                    db.Patients.FirstOrDefault(
                        p => p.Id == a.PatientId
                    );

                var doctor =
                    db.Doctors.FirstOrDefault(
                        d => d.Id == a.DoctorId
                    );

                Console.WriteLine(
                    $"ID: {a.Id} | " +
                    $"{a.Date:yyyy-MM-dd} {a.Time} | " +
                    $"Patient: {patient?.Name ?? "Unknown"} | " +
                    $"Dr. {doctor?.Name ?? "Unknown"} | " +
                    $"Status: {a.Status}"
                );

                Console.WriteLine(
                    $"Reason: {a.Reason}"
                );

                Console.WriteLine();
            }

            Pause();
        }

        static void CancelAppointment()
        {
            ViewAppointments();

            int id =
                ReadInt("Appointment ID: ");

            var appointment =
                db.Appointments.FirstOrDefault(
                    a => a.Id == id
                );

            if (appointment == null)
            {
                Console.WriteLine("Appointment not found.");
                Pause();
                return;
            }

            appointment.Status = "Cancelled";

            SaveData();

            Console.WriteLine(
                "Appointment cancelled."
            );

            Pause();
        }

        static void CompleteAppointment()
        {
            ViewAppointments();

            int id =
                ReadInt("Appointment ID: ");

            var appointment =
                db.Appointments.FirstOrDefault(
                    a => a.Id == id
                );

            if (appointment == null)
            {
                Console.WriteLine("Appointment not found.");
                Pause();
                return;
            }

            appointment.Status = "Completed";

            SaveData();

            Console.WriteLine(
                "Appointment completed."
            );

            Pause();
        }

        // =========================================================
        // ADMISSION / DISCHARGE
        // =========================================================

        static void AdmissionMenu()
        {
            while (true)
            {
                Console.Clear();
                Header();

                Console.WriteLine("ADMISSION & DISCHARGE");
                Console.WriteLine("---------------------");
                Console.WriteLine("1. Admit Patient");
                Console.WriteLine("2. View Admitted Patients");
                Console.WriteLine("3. Discharge Patient");
                Console.WriteLine("4. Admission History");
                Console.WriteLine("0. Back");

                Console.Write("\nEnter option: ");

                string choice =
                    Console.ReadLine() ?? "";

                switch (choice)
                {
                    case "1":
                        AdmitPatient();
                        break;

                    case "2":
                        ViewAdmittedPatients();
                        break;

                    case "3":
                        DischargePatient();
                        break;

                    case "4":
                        AdmissionHistory();
                        break;

                    case "0":
                        return;

                    default:
                        Console.WriteLine("Invalid option.");
                        Pause();
                        break;
                }
            }
        }

        static void AdmitPatient()
        {
            ViewPatients();

            int patientId =
                ReadInt("Patient ID: ");

            if (!db.Patients.Any(
                p => p.Id == patientId))
            {
                Console.WriteLine("Patient not found.");
                Pause();
                return;
            }

            if (db.Admissions.Any(
                a =>
                    a.PatientId == patientId &&
                    a.Status == "Admitted"))
            {
                Console.WriteLine(
                    "Patient is already admitted."
                );

                Pause();
                return;
            }

            ViewDoctors();

            int doctorId =
                ReadInt("Doctor ID: ");

            if (!db.Doctors.Any(
                d => d.Id == doctorId))
            {
                Console.WriteLine("Doctor not found.");
                Pause();
                return;
            }

            Console.Write("Room Number: ");
            string room =
                Console.ReadLine() ?? "";

            Console.Write("Bed Number: ");
            string bed =
                Console.ReadLine() ?? "";

            Console.Write("Diagnosis: ");
            string diagnosis =
                Console.ReadLine() ?? "";

            Admission admission =
                new Admission
                {
                    Id = NextAdmissionId(),
                    PatientId = patientId,
                    DoctorId = doctorId,
                    RoomNumber = room,
                    BedNumber = bed,
                    AdmissionDate = DateTime.Now,
                    Diagnosis = diagnosis,
                    Status = "Admitted"
                };

            db.Admissions.Add(admission);

            SaveData();

            Console.WriteLine(
                "Patient admitted successfully."
            );

            Pause();
        }

        static void ViewAdmittedPatients()
        {
            Console.Clear();
            Header();

            Console.WriteLine("CURRENTLY ADMITTED PATIENTS");
            Console.WriteLine();

            var admitted =
                db.Admissions
                .Where(a => a.Status == "Admitted")
                .ToList();

            if (!admitted.Any())
            {
                Console.WriteLine(
                    "No patients currently admitted."
                );

                Pause();
                return;
            }

            foreach (var a in admitted)
            {
                var patient =
                    db.Patients.FirstOrDefault(
                        p => p.Id == a.PatientId
                    );

                var doctor =
                    db.Doctors.FirstOrDefault(
                        d => d.Id == a.DoctorId
                    );

                Console.WriteLine(
                    $"Admission ID: {a.Id}"
                );

                Console.WriteLine(
                    $"Patient: {patient?.Name ?? "Unknown"}"
                );

                Console.WriteLine(
                    $"Doctor: Dr. {doctor?.Name ?? "Unknown"}"
                );

                Console.WriteLine(
                    $"Room: {a.RoomNumber} | Bed: {a.BedNumber}"
                );

                Console.WriteLine(
                    $"Admission Date: {a.AdmissionDate:yyyy-MM-dd}"
                );

                Console.WriteLine(
                    $"Diagnosis: {a.Diagnosis}"
                );

                Console.WriteLine(
                    new string('-', 50)
                );
            }

            Pause();
        }

        static void DischargePatient()
        {
            ViewAdmittedPatients();

            int id =
                ReadInt("Admission ID: ");

            var admission =
                db.Admissions.FirstOrDefault(
                    a => a.Id == id &&
                         a.Status == "Admitted"
                );

            if (admission == null)
            {
                Console.WriteLine(
                    "Active admission not found."
                );

                Pause();
                return;
            }

            admission.Status = "Discharged";
            admission.DischargeDate = DateTime.Now;

            SaveData();

            Console.WriteLine(
                "Patient discharged successfully."
            );

            Pause();
        }

        static void AdmissionHistory()
        {
            Console.Clear();
            Header();

            Console.WriteLine("ADMISSION HISTORY");
            Console.WriteLine();

            if (!db.Admissions.Any())
            {
                Console.WriteLine("No admission records.");
                Pause();
                return;
            }

            foreach (var a in db.Admissions)
            {
                var patient =
                    db.Patients.FirstOrDefault(
                        p => p.Id == a.PatientId
                    );

                Console.WriteLine(
                    $"ID: {a.Id} | " +
                    $"Patient: {patient?.Name ?? "Unknown"} | " +
                    $"Room: {a.RoomNumber} | " +
                    $"Status: {a.Status} | " +
                    $"Admitted: {a.AdmissionDate:yyyy-MM-dd} | " +
                    $"Discharged: " +
                    $"{(a.DischargeDate.HasValue
                        ? a.DischargeDate.Value.ToString("yyyy-MM-dd")
                        : "-")}"
                );
            }

            Pause();
        }

        // =========================================================
        // PRESCRIPTION
        // =========================================================

        static void PrescriptionMenu()
        {
            while (true)
            {
                Console.Clear();
                Header();

                Console.WriteLine("PRESCRIPTION MANAGEMENT");
                Console.WriteLine("-----------------------");
                Console.WriteLine("1. Add Prescription");
                Console.WriteLine("2. View Prescriptions");
                Console.WriteLine("3. Patient Prescriptions");
                Console.WriteLine("0. Back");

                Console.Write("\nEnter option: ");

                string choice =
                    Console.ReadLine() ?? "";

                switch (choice)
                {
                    case "1":
                        AddPrescription();
                        break;

                    case "2":
                        ViewPrescriptions();
                        break;

                    case "3":
                        PatientPrescriptions();
                        break;

                    case "0":
                        return;

                    default:
                        Console.WriteLine("Invalid option.");
                        Pause();
                        break;
                }
            }
        }

        static void AddPrescription()
        {
            ViewPatients();

            int patientId =
                ReadInt("Patient ID: ");

            if (!db.Patients.Any(
                p => p.Id == patientId))
            {
                Console.WriteLine("Patient not found.");
                Pause();
                return;
            }

            ViewDoctors();

            int doctorId =
                ReadInt("Doctor ID: ");

            if (!db.Doctors.Any(
                d => d.Id == doctorId))
            {
                Console.WriteLine("Doctor not found.");
                Pause();
                return;
            }

            Console.Write("Medicine: ");
            string medicine =
                Console.ReadLine() ?? "";

            Console.Write("Dosage: ");
            string dosage =
                Console.ReadLine() ?? "";

            Console.Write("Duration: ");
            string duration =
                Console.ReadLine() ?? "";

            Console.Write("Instructions: ");
            string instructions =
                Console.ReadLine() ?? "";

            db.Prescriptions.Add(
                new Prescription
                {
                    Id = NextPrescriptionId(),
                    PatientId = patientId,
                    DoctorId = doctorId,
                    Date = DateTime.Now,
                    Medicine = medicine,
                    Dosage = dosage,
                    Duration = duration,
                    Instructions = instructions
                }
            );

            SaveData();

            Console.WriteLine(
                "Prescription added successfully."
            );

            Pause();
        }

        static void ViewPrescriptions()
        {
            Console.Clear();
            Header();

            Console.WriteLine("ALL PRESCRIPTIONS");
            Console.WriteLine();

            if (!db.Prescriptions.Any())
            {
                Console.WriteLine(
                    "No prescriptions found."
                );

                Pause();
                return;
            }

            foreach (var p in db.Prescriptions
                .OrderByDescending(x => x.Date))
            {
                var patient =
                    db.Patients.FirstOrDefault(
                        x => x.Id == p.PatientId
                    );

                var doctor =
                    db.Doctors.FirstOrDefault(
                        x => x.Id == p.DoctorId
                    );

                Console.WriteLine(
                    $"ID: {p.Id} | " +
                    $"Date: {p.Date:yyyy-MM-dd}"
                );

                Console.WriteLine(
                    $"Patient: {patient?.Name ?? "Unknown"}"
                );

                Console.WriteLine(
                    $"Doctor: Dr. {doctor?.Name ?? "Unknown"}"
                );

                Console.WriteLine(
                    $"Medicine: {p.Medicine}"
                );

                Console.WriteLine(
                    $"Dosage: {p.Dosage}"
                );

                Console.WriteLine(
                    $"Duration: {p.Duration}"
                );

                Console.WriteLine(
                    $"Instructions: {p.Instructions}"
                );

                Console.WriteLine(
                    new string('-', 55)
                );
            }

            Pause();
        }

        static void PatientPrescriptions()
        {
            ViewPatients();

            int patientId =
                ReadInt("Patient ID: ");

            var prescriptions =
                db.Prescriptions
                .Where(p => p.PatientId == patientId)
                .OrderByDescending(p => p.Date)
                .ToList();

            Console.Clear();
            Header();

            Console.WriteLine("PATIENT PRESCRIPTIONS");
            Console.WriteLine();

            if (!prescriptions.Any())
            {
                Console.WriteLine(
                    "No prescriptions found."
                );

                Pause();
                return;
            }

            foreach (var p in prescriptions)
            {
                var doctor =
                    db.Doctors.FirstOrDefault(
                        d => d.Id == p.DoctorId
                    );

                Console.WriteLine(
                    $"Date: {p.Date:yyyy-MM-dd}"
                );

                Console.WriteLine(
                    $"Doctor: Dr. {doctor?.Name ?? "Unknown"}"
                );

                Console.WriteLine(
                    $"Medicine: {p.Medicine}"
                );

                Console.WriteLine(
                    $"Dosage: {p.Dosage}"
                );

                Console.WriteLine(
                    $"Duration: {p.Duration}"
                );

                Console.WriteLine(
                    $"Instructions: {p.Instructions}"
                );

                Console.WriteLine();
            }

            Pause();
        }

        // =========================================================
        // BILLING & PAYMENTS
        // =========================================================

        static void BillingMenu()
        {
            while (true)
            {
                Console.Clear();
                Header();

                Console.WriteLine("BILLING & PAYMENTS");
                Console.WriteLine("------------------");
                Console.WriteLine("1. Create Bill");
                Console.WriteLine("2. View Bills");
                Console.WriteLine("3. Make Payment");
                Console.WriteLine("4. Payment History");
                Console.WriteLine("5. Patient Billing Report");
                Console.WriteLine("6. Total Collection");
                Console.WriteLine("0. Back");

                Console.Write("\nEnter option: ");

                string choice =
                    Console.ReadLine() ?? "";

                switch (choice)
                {
                    case "1":
                        CreateBill();
                        break;

                    case "2":
                        ViewBills();
                        break;

                    case "3":
                        MakePayment();
                        break;

                    case "4":
                        PaymentHistory();
                        break;

                    case "5":
                        PatientBillingReport();
                        break;

                    case "6":
                        TotalCollection();
                        break;

                    case "0":
                        return;

                    default:
                        Console.WriteLine("Invalid option.");
                        Pause();
                        break;
                }
            }
        }

        static void CreateBill()
        {
            ViewPatients();

            int patientId =
                ReadInt("Patient ID: ");

            if (!db.Patients.Any(
                p => p.Id == patientId))
            {
                Console.WriteLine("Patient not found.");
                Pause();
                return;
            }

            Console.Write("Bill Type: ");
            string billType =
                Console.ReadLine() ?? "";

            double amount =
                ReadDouble("Amount: ");

            Bill bill =
                new Bill
                {
                    Id = NextBillId(),
                    BillNumber =
                        "BILL-" +
                        DateTime.Now.ToString("yyyyMMddHHmmss") +
                        "-" +
                        NextBillId(),

                    PatientId = patientId,
                    BillType = billType,
                    Amount = amount,
                    PaidAmount = 0,
                    Date = DateTime.Now,
                    Status = "Unpaid"
                };

            db.Bills.Add(bill);

            SaveData();

            Console.WriteLine();
            Console.WriteLine(
                "Bill created successfully."
            );

            Console.WriteLine(
                $"Bill Number: {bill.BillNumber}"
            );

            Pause();
        }

        static void ViewBills()
        {
            Console.Clear();
            Header();

            Console.WriteLine("BILLS");
            Console.WriteLine();

            if (!db.Bills.Any())
            {
                Console.WriteLine("No bills found.");
                Pause();
                return;
            }

            foreach (var bill in db.Bills
                .OrderByDescending(b => b.Date))
            {
                var patient =
                    db.Patients.FirstOrDefault(
                        p => p.Id == bill.PatientId
                    );

                double remaining =
                    bill.Amount - bill.PaidAmount;

                Console.WriteLine(
                    $"Bill: {bill.BillNumber}"
                );

                Console.WriteLine(
                    $"Patient: {patient?.Name ?? "Unknown"}"
                );

                Console.WriteLine(
                    $"Type: {bill.BillType}"
                );

                Console.WriteLine(
                    $"Total: {bill.Amount:C}"
                );

                Console.WriteLine(
                    $"Paid: {bill.PaidAmount:C}"
                );

                Console.WriteLine(
                    $"Remaining: {remaining:C}"
                );

                Console.WriteLine(
                    $"Status: {bill.Status}"
                );

                Console.WriteLine(
                    new string('-', 50)
                );
            }

            Pause();
        }

        static void MakePayment()
        {
            ViewBills();

            int billId =
                ReadInt("Bill ID: ");

            var bill =
                db.Bills.FirstOrDefault(
                    b => b.Id == billId
                );

            if (bill == null)
            {
                Console.WriteLine("Bill not found.");
                Pause();
                return;
            }

            double remaining =
                bill.Amount - bill.PaidAmount;

            Console.WriteLine(
                $"Remaining Amount: {remaining:C}"
            );

            if (remaining <= 0)
            {
                Console.WriteLine(
                    "This bill is already fully paid."
                );

                Pause();
                return;
            }

            double amount =
                ReadDouble("Payment Amount: ");

            if (amount <= 0 || amount > remaining)
            {
                Console.WriteLine(
                    "Invalid payment amount."
                );

                Pause();
                return;
            }

            Console.Write(
                "Payment Method (Cash/Card/Online): "
            );

            string method =
                Console.ReadLine() ?? "Cash";

            bill.PaidAmount += amount;

            if (bill.PaidAmount >= bill.Amount)
                bill.Status = "Paid";
            else
                bill.Status = "Partially Paid";

            Payment payment =
                new Payment
                {
                    Id = NextPaymentId(),
                    ReceiptNumber =
                        "REC-" +
                        DateTime.Now.ToString("yyyyMMddHHmmss") +
                        "-" +
                        NextPaymentId(),

                    PatientId = bill.PatientId,
                    BillId = bill.Id,
                    Amount = amount,
                    Date = DateTime.Now,
                    PaymentMethod = method
                };

            db.Payments.Add(payment);

            SaveData();

            Console.WriteLine();
            Console.WriteLine(
                "Payment successful."
            );

            Console.WriteLine(
                $"Receipt Number: {payment.ReceiptNumber}"
            );

            Console.WriteLine(
                $"Bill Status: {bill.Status}"
            );

            Pause();
        }

        static void PaymentHistory()
        {
            Console.Clear();
            Header();

            Console.WriteLine("PAYMENT HISTORY");
            Console.WriteLine();

            if (!db.Payments.Any())
            {
                Console.WriteLine("No payments found.");
                Pause();
                return;
            }

            foreach (var payment in
                db.Payments.OrderByDescending(p => p.Date))
            {
                var patient =
                    db.Patients.FirstOrDefault(
                        p => p.Id == payment.PatientId
                    );

                Console.WriteLine(
                    $"Receipt: {payment.ReceiptNumber}"
                );

                Console.WriteLine(
                    $"Patient: {patient?.Name ?? "Unknown"}"
                );

                Console.WriteLine(
                    $"Amount: {payment.Amount:C}"
                );

                Console.WriteLine(
                    $"Method: {payment.PaymentMethod}"
                );

                Console.WriteLine(
                    $"Date: {payment.Date:yyyy-MM-dd HH:mm}"
                );

                Console.WriteLine(
                    new string('-', 50)
                );
            }

            Pause();
        }

        static void PatientBillingReport()
        {
            ViewPatients();

            int patientId =
                ReadInt("Patient ID: ");

            var patient =
                db.Patients.FirstOrDefault(
                    p => p.Id == patientId
                );

            if (patient == null)
            {
                Console.WriteLine("Patient not found.");
                Pause();
                return;
            }

            var bills =
                db.Bills
                .Where(b => b.PatientId == patientId)
                .ToList();

            Console.Clear();
            Header();

            Console.WriteLine(
                $"BILLING REPORT - {patient.Name}"
            );

            Console.WriteLine();

            double total = bills.Sum(b => b.Amount);
            double paid = bills.Sum(b => b.PaidAmount);

            foreach (var bill in bills)
            {
                Console.WriteLine(
                    $"{bill.BillNumber} | " +
                    $"{bill.BillType} | " +
                    $"Total: {bill.Amount:C} | " +
                    $"Paid: {bill.PaidAmount:C} | " +
                    $"{bill.Status}"
                );
            }

            Console.WriteLine();
            Console.WriteLine(
                $"Total Bill: {total:C}"
            );

            Console.WriteLine(
                $"Total Paid: {paid:C}"
            );

            Console.WriteLine(
                $"Balance: {total - paid:C}"
            );

            Pause();
        }

        static void TotalCollection()
        {
            double total =
                db.Payments.Sum(p => p.Amount);

            Console.Clear();
            Header();

            Console.WriteLine("HOSPITAL COLLECTION");
            Console.WriteLine();

            Console.WriteLine(
                $"Total Payments : {db.Payments.Count}"
            );

            Console.WriteLine(
                $"Total Collection : {total:C}"
            );

            Pause();
        }

        // =========================================================
        // SEARCH
        // =========================================================

        static void SearchMenu()
        {
            while (true)
            {
                Console.Clear();
                Header();

                Console.WriteLine("SEARCH");
                Console.WriteLine("------");
                Console.WriteLine("1. Search Patient");
                Console.WriteLine("2. Search Doctor");
                Console.WriteLine("3. Search Appointment");
                Console.WriteLine("0. Back");

                Console.Write("\nEnter option: ");

                string choice =
                    Console.ReadLine() ?? "";

                switch (choice)
                {
                    case "1":
                        SearchPatient();
                        break;

                    case "2":
                        SearchDoctor();
                        break;

                    case "3":
                        SearchAppointment();
                        break;

                    case "0":
                        return;

                    default:
                        Console.WriteLine("Invalid option.");
                        Pause();
                        break;
                }
            }
        }

        static void SearchAppointment()
        {
            Console.Clear();
            Header();

            Console.Write(
                "Enter patient/doctor name: "
            );

            string search =
                Console.ReadLine() ?? "";

            var appointments =
                db.Appointments
                .Where(a =>
                {
                    var patient =
                        db.Patients.FirstOrDefault(
                            p => p.Id == a.PatientId
                        );

                    var doctor =
                        db.Doctors.FirstOrDefault(
                            d => d.Id == a.DoctorId
                        );

                    return
                        (patient?.Name ?? "")
                        .Contains(
                            search,
                            StringComparison.OrdinalIgnoreCase)
                        ||
                        (doctor?.Name ?? "")
                        .Contains(
                            search,
                            StringComparison.OrdinalIgnoreCase);
                })
                .ToList();

            Console.WriteLine();

            if (!appointments.Any())
            {
                Console.WriteLine(
                    "No appointment found."
                );
            }
            else
            {
                foreach (var a in appointments)
                {
                    var patient =
                        db.Patients.FirstOrDefault(
                            p => p.Id == a.PatientId
                        );

                    var doctor =
                        db.Doctors.FirstOrDefault(
                            d => d.Id == a.DoctorId
                        );

                    Console.WriteLine(
                        $"{a.Date:yyyy-MM-dd} {a.Time} | " +
                        $"{patient?.Name} | " +
                        $"Dr. {doctor?.Name} | " +
                        $"{a.Status}"
                    );
                }
            }

            Pause();
        }

        // =========================================================
        // DASHBOARD
        // =========================================================

        static void Dashboard()
        {
            Console.Clear();
            Header();

            Console.WriteLine("HOSPITAL DASHBOARD");
            Console.WriteLine("==================");
            Console.WriteLine();

            int currentAdmissions =
                db.Admissions.Count(
                    a => a.Status == "Admitted"
                );

            int scheduledAppointments =
                db.Appointments.Count(
                    a => a.Status == "Scheduled"
                );

            double collection =
                db.Payments.Sum(p => p.Amount);

            double totalBills =
                db.Bills.Sum(b => b.Amount);

            double paidBills =
                db.Bills.Sum(b => b.PaidAmount);

            Console.WriteLine(
                $"Total Patients       : {db.Patients.Count}"
            );

            Console.WriteLine(
                $"Total Doctors        : {db.Doctors.Count}"
            );

            Console.WriteLine(
                $"Total Appointments   : {db.Appointments.Count}"
            );

            Console.WriteLine(
                $"Scheduled Appointments: {scheduledAppointments}"
            );

            Console.WriteLine(
                $"Current Admissions   : {currentAdmissions}"
            );

            Console.WriteLine(
                $"Prescriptions        : {db.Prescriptions.Count}"
            );

            Console.WriteLine(
                $"Total Bills          : {db.Bills.Count}"
            );

            Console.WriteLine(
                $"Total Bill Amount    : {totalBills:C}"
            );

            Console.WriteLine(
                $"Total Paid           : {paidBills:C}"
            );

            Console.WriteLine(
                $"Total Collection     : {collection:C}"
            );

            Console.WriteLine(
                $"Outstanding Balance  : {(totalBills - paidBills):C}"
            );

            Pause();
        }

        // =========================================================
        // DATA STORAGE
        // =========================================================

        static void SaveData()
        {
            try
            {
                string json =
                    JsonSerializer.Serialize(
                        db,
                        jsonOptions
                    );

                File.WriteAllText(
                    dataFile,
                    json
                );
            }
            catch (Exception ex)
            {
                Console.WriteLine(
                    "Error saving data: " + ex.Message
                );
            }
        }

        static void LoadData()
        {
            try
            {
                if (!File.Exists(dataFile))
                    return;

                string json =
                    File.ReadAllText(dataFile);

                HospitalData? loaded =
                    JsonSerializer.Deserialize<HospitalData>(
                        json
                    );

                if (loaded != null)
                    db = loaded;
            }
            catch
            {
                db = new HospitalData();
            }
        }

        // =========================================================
        // ID GENERATORS
        // =========================================================

        static int NextPatientId()
        {
            return db.Patients.Any()
                ? db.Patients.Max(p => p.Id) + 1
                : 1;
        }

        static int NextDoctorId()
        {
            return db.Doctors.Any()
                ? db.Doctors.Max(d => d.Id) + 1
                : 1;
        }

        static int NextAppointmentId()
        {
            return db.Appointments.Any()
                ? db.Appointments.Max(a => a.Id) + 1
                : 1;
        }

        static int NextAdmissionId()
        {
            return db.Admissions.Any()
                ? db.Admissions.Max(a => a.Id) + 1
                : 1;
        }

        static int NextPrescriptionId()
        {
            return db.Prescriptions.Any()
                ? db.Prescriptions.Max(p => p.Id) + 1
                : 1;
        }

        static int NextBillId()
        {
            return db.Bills.Any()
                ? db.Bills.Max(b => b.Id) + 1
                : 1;
        }

        static int NextPaymentId()
        {
            return db.Payments.Any()
                ? db.Payments.Max(p => p.Id) + 1
                : 1;
        }

        // =========================================================
        // INPUT HELPERS
        // =========================================================

        static int ReadInt(string message)
        {
            while (true)
            {
                Console.Write(message);

                if (int.TryParse(
                    Console.ReadLine(),
                    out int value))
                {
                    return value;
                }

                Console.WriteLine(
                    "Please enter a valid number."
                );
            }
        }

        static double ReadDouble(string message)
        {
            while (true)
            {
                Console.Write(message);

                if (double.TryParse(
                    Console.ReadLine(),
                    out double value))
                {
                    return value;
                }

                Console.WriteLine(
                    "Please enter a valid amount."
                );
            }
        }

        static void Pause()
        {
            Console.WriteLine();
            Console.WriteLine(
                "Press ENTER to continue..."
            );

            Console.ReadLine();
        }
    }
}## Hi there 👋

<!--
**Abdullah837837/abdullah837837** is a ✨ _special_ ✨ repository because its `README.md` (this file) appears on your GitHub profile.

Here are some ideas to get you started:

- 🔭 I’m currently working on ...
- 🌱 I’m currently learning ...
- 👯 I’m looking to collaborate on ...
- 🤔 I’m looking for help with ...
- 💬 Ask me about ...
- 📫 How to reach me: ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...
-->
