import os
import time
import datetime
from datetime import datetime
import shutil
import wx
import sqlite3

conn=sqlite3.connect('file_transfer.db')
c = conn.cursor()

global main, source
source_folder = []
new_files = []

class Frame(wx.Frame):
    def __init__(self, parent, ID, title):
        wx.Frame.__init__(self, None, -1, title)

        global panel, last_run
        panel = wx.Panel(self)

        my_db_data = c.execute("SELECT timestamp FROM transfers")
        for i in my_db_data:
            wx.StaticText(panel, -1, 'Previous transfer date: %s' %i, (10,10))

        last_run = datetime.strptime('%s'%i, '%Y-%m-%d %H:%M:%S')
                
        source_button = wx.Button(panel, label="Browse source\n folder", pos=(65, 50), size=(100,100))
        dest_button = wx.Button(panel, label="Browse\n destination\n folder", pos=(225, 50), size=(100,100))
        main_office_button = wx.Button(panel, label="Send modified\n files to \nmain office \nfolder", pos=(115, 200), size=(150,150))

        self.Bind(wx.EVT_BUTTON, self.source_dir, source_button)
        self.Bind(wx.EVT_BUTTON, self.dest_dir, dest_button)
        self.Bind(wx.EVT_BUTTON, self.send_to_main, main_office_button)

    def source_dir(self,event):
        global source, fileList, full_file_path
        dialog = wx.DirDialog(None, "Choose a directory:",style=wx.DD_DEFAULT_STYLE | wx.DD_NEW_DIR_BUTTON)


        if dialog.ShowModal() == wx.ID_OK:
            source = dialog.GetPath()


            def get_all_files(source):
                global fileList
                dir_listing = os.listdir(source)
                for file in dir_listing:
                    source_folder.append(file)
                fileList = source_folder
            get_all_files(source)


            def send_files(fileList):
                global full_file_path, current_time, i, last_run
                current_time = datetime.now()
                for file in fileList:
                    full_file_path = source + "\\" + file
                    delta_time = current_time - datetime.fromtimestamp(os.path.getmtime(full_file_path))
                    if delta_time < current_time -last_run:
                        new_files.append(file)
            send_files(fileList)

        dialog.Destroy()



    def dest_dir(self,event):
        global main, panel
        dialog = wx.DirDialog(None, "Choose a directory:",style=wx.DD_DEFAULT_STYLE | wx.DD_NEW_DIR_BUTTON)
        if dialog.ShowModal() == wx.ID_OK:
            main = dialog.GetPath()
        dialog.Destroy()

    def send_to_main(self,event):
        global main, source, full_file_path, current_time
        for file in new_files:
            shutil.copy(full_file_path, main + "\\" + file)
        c.execute("INSERT INTO transfers(timestamp) VALUES (DATETIME('now', 'localtime'))")
        conn.commit()
        conn.close()
        my_db_data = c.execute("SELECT timestamp FROM transfers")
        for i in my_db_data:
            wx.StaticText(panel, -1, 'Previous transfer date: %s' %i, (10,10))
        
        
        
        
       

app = wx.App(False)

frame = Frame(None, -1, 'Transfers')
frame.Show()

app.MainLoop()
