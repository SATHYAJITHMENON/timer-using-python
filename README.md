# timer-using-python

import time
from tkinter import *
import multiprocessing
from tkinter import ttk, messagebox
from threading import *


hour_list = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 
15, 16, 17, 18, 19, 20, 21, 22, 23, 24]


min_sec_list = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 
15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 
30, 31, 32, 33, 34, 35, 36, 37, 38, 39, 40, 41, 42, 43, 44,
45, 46, 47, 48, 49, 50, 51, 52, 53, 54, 55, 56, 57, 58, 59, 
]


class CountDown:
    def __init__(self, root):
        self.window = root
        self.window.geometry("480x320+0+0")
        self.window.title('CountDown Timer')

        self.window.configure(bg='gray35')
        self.window.resizable(width = False, height = False)

        self.pause = False

        self.button_frame = Frame(self.window, bg="gray35", \
        width=240, height=40)
        self.button_frame.place(x=230, y=150)
        self.time_frame = Frame(self.window, bg="gray35", \
        width=480, height=120).place(x=0, y=210)

        time_label = Label(self.window, text="Set Time", 
        font=("times new roman",20, "bold"), bg='gray35',fg='yellow')
        time_label.place(x=180, y=30)

        hour_label = Label(self.window, text="Hour", 
        font=("times new roman",15), bg='gray35', fg='white')
        hour_label.place(x=50, y=70)

        minute_label = Label(self.window, text="Minute", 
        font=("times new roman",15), bg='gray35', fg='white')
        minute_label.place(x=200, y=70)

        second_label = Label(self.window, text="Second", 
        font=("times new roman",15), bg='gray35', fg='white')
        second_label.place(x=350, y=70)
        self.hour = IntVar()
        self.hour_combobox = ttk.Combobox(self.window, width=8, 
        height=10, textvariable=self.hour, 
        font=("times new roman",15))
        self.hour_combobox['values'] = hour_list
        self.hour_combobox.current(0)
        self.hour_combobox.place(x=50,y=110)

        self.minute = IntVar()
        self.minute_combobox = ttk.Combobox(self.window, width=8, 
        height=10, textvariable=self.minute, 
        font=("times new roman",15))
        self.minute_combobox['values'] = min_sec_list
        self.minute_combobox.current(0)
        self.minute_combobox.place(x=200,y=110)

      
        self.second = IntVar()
        self.second_combobox = ttk.Combobox(self.window, width=8, 
        height=10, textvariable=self.second, 
        font=("times new roman",15))
        self.second_combobox['values'] = min_sec_list
        self.second_combobox.current(0)
        self.second_combobox.place(x=350,y=110)
        
        cancel_button = Button(self.window, text='Cancel', 
        font=('Helvetica',12), bg="white", fg="black", 
        command=self.Cancel)
        cancel_button.place(x=70, y=150)

        
        set_button = Button(self.window, text='Set', 
        font=('Helvetica',12), bg="white", fg="black", 
        command=self.Get_Time)
        set_button.place(x=160, y=150)

    def Cancel(self):
        self.pause = True
        self.window.destroy()
    
    def Get_Time(self):
        self.time_display = Label(self.time_frame, 
        font=('Helvetica', 20 , "bold"), 
        bg = 'gray35', fg = 'yellow')
        self.time_display.place(x=130, y=210)

        try:
            h = (int(self.hour_combobox.get())*3600)
            m = (int(self.minute_combobox.get())*60)
            s = (int(self.second_combobox.get()))
            self.time_left = h + m + s

            if s == 0 and m == 0 and h == 0:
                messagebox.showwarning('Warning!',\
                'Please select a right time to set')
            else:
                start_button = Button(self.button_frame, text='Start', 
                font=('Helvetica',12), bg="green", fg="white", 
                command=self.Threading)
                start_button.place(x=20, y=0)

                pause_button = Button(self.button_frame, text='Pause', 
                font=('Helvetica',12), bg="red", fg="white",
                command=self.pause_time)
                pause_button.place(x=100, y=0)
        except Exception as es:
            messagebox.showerror("Error!", \
            f"Error due to {es}")

    def Threading(self):
        self.x = Thread(target=self.start_time, daemon=True)
        self.x.start()

    def Clear_Screen(self):
        for widget in self.button_frame.winfo_children():
            widget.destroy()

    def pause_time(self):
        self.pause = True

        mins, secs = divmod(self.time_left, 60)
        hours = 0
        if mins > 60:
            hours, mins = divmod(mins, 60)

        self.time_display.config(text=f"Time Left: {hours}: {mins}: {secs}")
        self.time_display.update()
    def start_time(self):
        self.pause = False
        while self.time_left > 0:
            mins, secs = divmod(self.time_left, 60)
            hours = 0
            if mins > 60:
                hours, mins = divmod(mins, 60)

            self.time_display.config(text=f"Time Left: {hours}: {mins}: {secs}")
            self.time_display.update()
            time.sleep(1)
            self.time_left = self.time_left -1
            if self.time_left <= 0:
                process = multiprocessing.Process(target=playsound, 
                args=('Ringtones/romantic.mp3',))
                process.start()
                messagebox.showinfo('Time Over','Please ENTER to stop playing')
                process.terminate()
            if self.pause == True:
                break

if __name__ == "__main__":
    root = Tk()
    obj = CountDown(root)
    root.mainloop()
