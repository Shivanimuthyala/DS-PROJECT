import tkinter as tk
from tkinter import messagebox

# Dictionary to store information about trains
trains = {}


def add_train(train_number, total_seats):
    try:
        train_number = int(train_number)
        total_seats = int(total_seats)
        if train_number < 1 or total_seats < 1:
            raise ValueError("Train number and total seats must be positive integers.")

        trains[train_number] = {"total_seats": total_seats, "available_seats": total_seats, "reserved_seats": {}}
        messagebox.showinfo("Success", "Train added successfully.")
    except ValueError as e:
        messagebox.showerror("Error", str(e))


def display_trains():
    display_window = tk.Toplevel(root)
    display_window.title("Display Trains")

    trains_text = ""
    for train_number, train_info in trains.items():
        trains_text += f"Train {train_number} - Available Seats: {train_info['available_seats']}/{train_info['total_seats']}\n"

    display_label = tk.Label(display_window, text=trains_text)
    display_label.pack(pady=10)


def reserve_seat(train_number, num_seats, passenger_name, mobile_number):
    try:
        train_number = int(train_number)
        num_seats = int(num_seats)
        if train_number not in trains:
            raise ValueError(f"Train {train_number} not found.")

        train_info = trains[train_number]

        if 1 <= num_seats <= train_info["available_seats"]:
            for _ in range(num_seats):
                seat_number = get_available_seat(train_info)
                train_info["reserved_seats"][seat_number] = {"Passenger Name": passenger_name, "Mobile Number": mobile_number}

            train_info["available_seats"] -= num_seats
            messagebox.showinfo("Success", "Seats reserved successfully.")
        else:
            raise ValueError("Invalid number of seats or not enough seats available.")
    except ValueError as e:
        messagebox.showerror("Error", str(e))


def get_available_seat(train_info):
    for seat in range(1, train_info["total_seats"] + 1):
        if str(seat) not in train_info["reserved_seats"]:
            return str(seat)
    raise ValueError("No available seats found.")


def display_reserved_seats(train_number):
    try:
        train_number = int(train_number)
        if train_number not in trains:
            raise ValueError(f"Train {train_number} not found.")

        reserved_seats_info = trains[train_number]["reserved_seats"]
        reserved_seats_text = "\n".join([f"Seat {seat}: {info}" for seat, info in reserved_seats_info.items()])

        reserved_seats_window = tk.Toplevel(root)
        reserved_seats_window.title(f"Reserved Seats for Train {train_number}")

        reserved_seats_label = tk.Label(reserved_seats_window, text=reserved_seats_text)
        reserved_seats_label.pack(pady=10)
    except ValueError as e:
        messagebox.showerror("Error", str(e))


def display_leftover_seats(train_number):
    try:
        train_number = int(train_number)
        if train_number not in trains:
            raise ValueError(f"Train {train_number} not found.")

        leftover_seats = trains[train_number]["available_seats"]
        messagebox.showinfo("Leftover Seats", f"Train {train_number} - Leftover Seats: {leftover_seats}")
    except ValueError as e:
        messagebox.showerror("Error", str(e))


def cancel_reserved_seats(train_number, seat_numbers):
    try:
        train_number = int(train_number)
        if train_number not in trains:
            raise ValueError(f"Train {train_number} not found.")

        train_info = trains[train_number]

        for seat_number in seat_numbers:
            if seat_number in train_info["reserved_seats"]:
                del train_info["reserved_seats"][seat_number]
                train_info["available_seats"] += 1
            else:
                raise ValueError(f"Seat {seat_number} not found or not reserved.")

        messagebox.showinfo("Success", "Seats cancelled successfully.")
    except ValueError as e:
        messagebox.showerror("Error", str(e))


def show_add_train_window():
    add_train_window = tk.Toplevel(root)
    add_train_window.title("Add Train")

    train_number_label = tk.Label(add_train_window, text="Train Number:")
    train_number_label.grid(row=0, column=0)

    train_number_entry = tk.Entry(add_train_window)
    train_number_entry.grid(row=0, column=1)

    total_seats_label = tk.Label(add_train_window, text="Total Seats:")
    total_seats_label.grid(row=1, column=0)

    total_seats_entry = tk.Entry(add_train_window)
    total_seats_entry.grid(row=1, column=1)

    add_train_button = tk.Button(add_train_window, text="Add Train",
                                 command=lambda: add_train(train_number_entry.get(), total_seats_entry.get()))
    add_train_button.grid(row=2, columnspan=2, pady=10)


def show_reserve_seat_window():
    reserve_seat_window = tk.Toplevel(root)
    reserve_seat_window.title("Reserve Seat")

    train_number_label = tk.Label(reserve_seat_window, text="Train Number:")
    train_number_label.grid(row=0, column=0)

    train_number_entry = tk.Entry(reserve_seat_window)
    train_number_entry.grid(row=0, column=1)

    num_seats_label = tk.Label(reserve_seat_window, text="Number of Seats:")
    num_seats_label.grid(row=1, column=0)

    num_seats_entry = tk.Entry(reserve_seat_window)
    num_seats_entry.grid(row=1, column=1)

    passenger_name_label = tk.Label(reserve_seat_window, text="Passenger Name:")
    passenger_name_label.grid(row=2, column=0)

    passenger_name_entry = tk.Entry(reserve_seat_window)
    passenger_name_entry.grid(row=2, column=1)

    mobile_number_label = tk.Label(reserve_seat_window, text="Mobile Number:")
    mobile_number_label.grid(row=3, column=0)

    mobile_number_entry = tk.Entry(reserve_seat_window)
    mobile_number_entry.grid(row=3, column=1)

    reserve_seat_button = tk.Button(reserve_seat_window, text="Reserve Seat",
                                    command=lambda: reserve_seat(train_number_entry.get(), num_seats_entry.get(),
                                                                passenger_name_entry.get(), mobile_number_entry.get()))
    reserve_seat_button.grid(row=4, columnspan=2, pady=10)


def show_reserved_seats_window():
    reserved_seats_window = tk.Toplevel(root)
    reserved_seats_window.title("Display Reserved Seats")

    train_number_label = tk.Label(reserved_seats_window, text="Train Number:")
    train_number_label.grid(row=0, column=0)

    train_number_entry = tk.Entry(reserved_seats_window)
    train_number_entry.grid(row=0, column=1)

    display_button = tk.Button(reserved_seats_window, text="Display Reserved Seats",
                               command=lambda: display_reserved_seats(train_number_entry.get()))
    display_button.grid(row=1, columnspan=2, pady=10)


def show_leftover_seats_window():
    leftover_seats_window = tk.Toplevel(root)
    leftover_seats_window.title("Display Leftover Seats")

    train_number_label = tk.Label(leftover_seats_window, text="Train Number:")
    train_number_label.grid(row=0, column=0)

    train_number_entry = tk.Entry(leftover_seats_window)
    train_number_entry.grid(row=0, column=1)

    display_button = tk.Button(leftover_seats_window, text="Display Leftover Seats",
                               command=lambda: display_leftover_seats(train_number_entry.get()))
    display_button.grid(row=1, columnspan=2, pady=10)


def show_cancel_reserved_seats_window():
    cancel_reserved_seats_window = tk.Toplevel(root)
    cancel_reserved_seats_window.title("Cancel Reserved Seats")

    train_number_label = tk.Label(cancel_reserved_seats_window, text="Train Number:")
    train_number_label.grid(row=0, column=0)

    train_number_entry = tk.Entry(cancel_reserved_seats_window)
    train_number_entry.grid(row=0, column=1)

    seat_numbers_label = tk.Label(cancel_reserved_seats_window, text="Seat Numbers (comma-separated):")
    seat_numbers_label.grid(row=1, column=0)

    seat_numbers_entry = tk.Entry(cancel_reserved_seats_window)
    seat_numbers_entry.grid(row=1, column=1)

    cancel_seats_button = tk.Button(cancel_reserved_seats_window, text="Cancel Reserved Seats",
                                    command=lambda: cancel_reserved_seats(train_number_entry.get(),
                                                                         seat_numbers_entry.get().split(',')))
    cancel_seats_button.grid(row=2, columnspan=2, pady=10)


if __name__ == "__main__":
    root = tk.Tk()
    root.title("Train Reservation System")

    label = tk.Label(root, text="Train Reservation System", font=("Helvetica", 16))
    label.pack(pady=10)

    add_train_button = tk.Button(root, text="Add Train", command=show_add_train_window)
    add_train_button.pack(pady=10)

    display_trains_button = tk.Button(root, text="Display Trains", command=display_trains)
    display_trains_button.pack(pady=10)

    reserve_seat_button = tk.Button(root, text="Reserve Seat", command=show_reserve_seat_window)
    reserve_seat_button.pack(pady=10)

    reserved_seats_button = tk.Button(root, text="Display Reserved Seats", command=show_reserved_seats_window)
    reserved_seats_button.pack(pady=10)

    leftover_seats_button = tk.Button(root, text="Display Leftover Seats", command=show_leftover_seats_window)
    leftover_seats_button.pack(pady=10)

    cancel_reserved_seats_button = tk.Button(root, text="Cancel Reserved Seats", command=show_cancel_reserved_seats_window)
    cancel_reserved_seats_button.pack(pady=10)

    root.mainloop()

