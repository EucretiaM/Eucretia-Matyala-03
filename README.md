# Eucretia-Matyala-03
LOG IN TRACKER
import time

class LoginSystem:
    def __init__(self):
        self.users = {
            "user1": {"password": "password123", "failed_attempts": 0, "locked": False, "lock_time": None},
            "user2": {"password": "password456", "failed_attempts": 0, "locked": False, "lock_time": None}
        }
        self.max_failed_attempts = 3  # Max failed attempts before lock
        self.lock_duration = 300  # Lock duration in seconds (5 minutes)

    def login(self, username, password):
        # Check if username exists
        if username not in self.users:
            print("Username not found!")
            return

        user = self.users[username]

        # If the account is locked, check if lock duration has passed
        if user["locked"]:
            lock_time_left = self.lock_duration - (time.time() - user["lock_time"])
            if lock_time_left > 0:
                print(f"Account is locked. Try again in {int(lock_time_left)} seconds.")
                return
            else:
                # Unlock the account if lock time has expired
                user["locked"] = False
                user["failed_attempts"] = 0
                user["lock_time"] = None

        # Check password
        if user["password"] == password:
            print("Login successful!")
            user["failed_attempts"] = 0  # Reset failed attempts on successful login
        else:
            print("Incorrect password.")
            user["failed_attempts"] += 1
            # Lock the account after maximum failed attempts
            if user["failed_attempts"] >= self.max_failed_attempts:
                user["locked"] = True
                user["lock_time"] = time.time()  # Set the time when the account was locked
                print(f"Too many failed attempts. Account locked for {self.lock_duration} seconds.")

    def reset_failed_attempts(self, username):
        """Reset the failed attempts counter (admin function)"""
        if username in self.users:
            self.users[username]["failed_attempts"] = 0
            self.users[username]["locked"] = False
            self.users[username]["lock_time"] = None
            print(f"Failed attempts for {username} have been reset.")
        else:
            print(f"Username {username} not found.")


# Example usage:
login_system = LoginSystem()

# Simulate login attempts
login_system.login("user1", "wrongpassword")  # Failed attempt
login_system.login("user1", "wrongpassword")  # Failed attempt
login_system.login("user1", "wrongpassword")  # Failed attempt (Account lock triggered)

# After some time, we can try again (let's simulate waiting for unlock)
time.sleep(3)  # Simulate some waiting time for simplicity
login_system.login("user1", "password123")  # Successful login after lock expires
